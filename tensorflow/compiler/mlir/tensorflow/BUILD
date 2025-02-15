load("@local_config_mlir//:tblgen.bzl", "gentbl")
load("//tensorflow:tensorflow.bzl", "tf_cc_test", "tf_gen_op_wrapper_py", "tf_native_cc_binary")

package(
    default_visibility = [":friends"],
    licenses = ["notice"],  # Apache 2.0
)

package_group(
    name = "friends",
    includes = ["@local_config_mlir//:subpackages"],
    packages = [
        "//tensorflow/compiler/...",
        "//tensorflow/python/...",
    ],
)

filegroup(
    name = "tensorflow_ops_td_files",
    srcs = [
        "ir/tf_generated_ops.td",
        "ir/tf_op_base.td",
        "ir/tf_ops.td",
        "@local_config_mlir//:OpBaseTdFiles",
    ],
)

gentbl(
    name = "tensorflow_ops_inc_gen",
    tbl_outs = [
        (
            "-gen-op-decls",
            "ir/tf_ops.h.inc",
        ),
        (
            "-gen-op-defs",
            "ir/tf_ops.cc.inc",
        ),
        (
            "-gen-op-doc",
            "g3doc/tf_ops.md",
        ),
    ],
    tblgen = "@local_config_mlir//:mlir-tblgen",
    td_file = "ir/tf_ops.td",
    td_srcs = [
        ":tensorflow_ops_td_files",
    ],
)

gentbl(
    name = "tensorflow_executor_inc_gen",
    tbl_outs = [
        (
            "-gen-op-decls",
            "ir/tf_executor.h.inc",
        ),
        (
            "-gen-op-defs",
            "ir/tf_executor.cc.inc",
        ),
        (
            "-gen-op-doc",
            "g3doc/tf_executor.md",
        ),
    ],
    tblgen = "@local_config_mlir//:mlir-tblgen",
    td_file = "ir/tf_executor_ops.td",
    td_srcs = [
        "@local_config_mlir//:include/mlir/IR/OpBase.td",
        "@local_config_mlir//:include/mlir/Dialect/StandardOps/Ops.td",
    ],
)

gentbl(
    name = "tensorflow_device_ops_inc_gen",
    tbl_outs = [
        (
            "-gen-op-decls",
            "ir/tf_device.h.inc",
        ),
        (
            "-gen-op-defs",
            "ir/tf_device.cc.inc",
        ),
        (
            "-gen-op-doc",
            "g3doc/tf_device.md",
        ),
    ],
    tblgen = "@local_config_mlir//:mlir-tblgen",
    td_file = "ir/tf_device_ops.td",
    td_srcs = [
        "@local_config_mlir//:include/mlir/IR/OpBase.td",
        "@local_config_mlir//:include/mlir/Dialect/StandardOps/Ops.td",
    ],
)

gentbl(
    name = "tensorflow_canonicalize_inc_gen",
    tbl_outs = [
        (
            "-gen-rewriters",
            "transforms/generated_canonicalize.inc",
        ),
    ],
    tblgen = "@local_config_mlir//:mlir-tblgen",
    td_file = "transforms/canonicalize.td",
    td_srcs = [
        ":tensorflow_ops_td_files",
    ],
)

cc_library(
    name = "tensorflow",
    srcs = [
        "ir/control_flow_ops.cc",
        "ir/tf_device.cc",
        "ir/tf_executor.cc",
        "ir/tf_executor.cc.inc",
        "ir/tf_executor.h.inc",
        "ir/tf_ops.cc",
        "ir/tf_ops.cc.inc",
        "ir/tf_ops.h.inc",
        "ir/tf_types.cc",
    ],
    hdrs = [
        "ir/control_flow_ops.h",
        "ir/tf_device.h",
        "ir/tf_executor.h",
        "ir/tf_ops.h",
        "ir/tf_traits.h",
        "ir/tf_types.def",
        "ir/tf_types.h",
        "transforms/bridge.h",
        "transforms/passes.h",
    ],
    includes = ["include"],
    deps = [
        ":error_util",
        ":mlir_passthrough_op",
        ":tensorflow_canonicalize_inc_gen",
        ":tensorflow_device_ops_inc_gen",
        ":tensorflow_executor_inc_gen",
        ":tensorflow_ops_inc_gen",
        "//tensorflow/compiler/mlir/lite:validators",
        "//tensorflow/core:lib",
        "@llvm//:support",
        "@local_config_mlir//:Analysis",
        "@local_config_mlir//:Dialect",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Parser",
        "@local_config_mlir//:Pass",
        "@local_config_mlir//:StandardOps",
        "@local_config_mlir//:Support",
        "@local_config_mlir//:TransformUtils",
        "@local_config_mlir//:Transforms",
    ],
    # TODO(jpienaar): Merge in the dialect registration.
    alwayslink = 1,
)

cc_library(
    name = "tensorflow_passes",
    srcs = [
        "transforms/bridge.cc",
        "transforms/bridge_pass.cc",
        "transforms/cluster_formation.cc",
        "transforms/cluster_outlining.cc",
        "transforms/executor_island_coarsening.cc",
        "transforms/fold_switch.cc",
        "transforms/functional_control_flow_to_cfg.cc",
        "transforms/generated_canonicalize.inc",
        "transforms/generated_optimize.inc",
        "transforms/graph_pruning.cc",
        "transforms/materialize_mlir_passthrough_op.cc",
        "transforms/optimize.cc",
        "transforms/raise_control_flow.cc",
        "transforms/shape_inference.cc",
        "transforms/shape_inference_pass.cc",
        "transforms/sink_constant.cc",
        "transforms/tpu_cluster_formation.cc",
        "transforms/tpu_rewrite_pass.cc",
        "translate/control_to_executor_dialect.cc",
        "translate/executor_to_control_dialect.cc",
    ],
    hdrs = [
        "transforms/bridge.h",
        "transforms/passes.h",
        "transforms/shape_inference.h",
    ],
    includes = ["include"],
    deps = [
        ":convert_tensor",
        ":convert_type",
        ":error_util",
        ":export_tf_dialect_op",
        ":tensorflow",
        ":tensorflow_optimize_inc_gen",
        "//tensorflow/compiler/mlir/lite:validators",
        "//tensorflow/compiler/xla:xla_data_proto",
        "//tensorflow/compiler/xla:xla_proto",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/core/platform:logging",
        "//tensorflow/core/protobuf/tpu:compile_metadata_proto",
        "//tensorflow/core/protobuf/tpu:dynamic_padding_proto_cc",
        "@llvm//:support",
        "@local_config_mlir//:Analysis",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Parser",
        "@local_config_mlir//:Pass",
        "@local_config_mlir//:StandardOps",
        "@local_config_mlir//:Support",
        "@local_config_mlir//:TransformUtils",
        "@local_config_mlir//:Transforms",
    ],
    # TODO(jpienaar): Merge in the dialect registration.
    alwayslink = 1,
)

cc_library(
    name = "tensorflow_test_passes",
    srcs = [
        "transforms/lower_tf_pass.cc",
    ],
    deps = [
        ":lower_tf_lib",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Pass",
    ],
    alwayslink = 1,
)

# Library with TensorFlow dialect static initialization.
cc_library(
    name = "tensorflow_dialect_registration",
    srcs = ["ir/dialect_registration.cc"],
    deps = [
        ":tensorflow",
        "@local_config_mlir//:IR",
    ],
    alwayslink = 1,
)

cc_library(
    name = "convert_graphdef",
    srcs = [
        "translate/export_graphdef.cc",
        "translate/import_model.cc",
    ],
    hdrs = [
        "translate/export_graphdef.h",
        "translate/import_model.h",
    ],
    deps = [
        ":convert_tensor",
        ":convert_type",
        ":export_tf_dialect_op",
        ":export_utils",
        ":mangling_util",
        ":mlir_roundtrip_flags",
        ":tensorflow",
        ":tensorflow_passes",
        "//tensorflow/cc/saved_model:loader_lite",
        "//tensorflow/compiler/jit:shape_inference_helpers",
        "//tensorflow/compiler/tf2xla:functionalize_control_flow",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:graph",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/core/platform:types",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:optional",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Pass",
        "@local_config_mlir//:StandardDialectRegistration",
        "@local_config_mlir//:StandardOps",
        "@local_config_mlir//:Support",
    ],
)

cc_library(
    name = "import_utils",
    srcs = [
        "utils/import_utils.cc",
    ],
    hdrs = [
        "utils/import_utils.h",
    ],
    deps = [
        ":error_util",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
    ],
)

cc_library(
    name = "export_utils",
    srcs = [
        "utils/export_utils.cc",
    ],
    hdrs = [
        "ir/tf_types.def",
        "ir/tf_types.h",
        "utils/export_utils.h",
    ],
    deps = [
        ":convert_tensor",
        ":convert_type",
        ":mangling_util",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:graph",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:StandardDialectRegistration",
        "@local_config_mlir//:StandardOps",
        "@local_config_mlir//:Support",
    ],
)

cc_library(
    name = "export_tf_dialect_op",
    srcs = [
        "translate/derived_attr_populator.inc",
        "translate/export_tf_dialect_op.cc",
    ],
    hdrs = [
        "translate/export_tf_dialect_op.h",
    ],
    deps = [
        ":convert_type",
        ":export_utils",
        ":tensorflow",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
        "@local_config_mlir//:IR",
    ],
)

cc_library(
    name = "translate_tf_dialect_op",
    srcs = ["translate/translate_tf_dialect_op.cc"],
    deps = [
        ":export_tf_dialect_op",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Support",
        "@local_config_mlir//:Translation",
    ],
    alwayslink = 1,
)

cc_library(
    name = "mlir_roundtrip_pass",
    srcs = ["translate/mlir_roundtrip_pass.cc"],
    hdrs = ["translate/mlir_roundtrip_pass.h"],
    deps = [
        ":convert_graphdef",
        ":error_util",
        ":mlir_roundtrip_flags",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/core:core_cpu_lib",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "@local_config_mlir//:Analysis",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:StandardOps",
    ],
    alwayslink = 1,
)

cc_library(
    name = "mlir_roundtrip_flags",
    srcs = ["translate/mlir_roundtrip_flags.cc"],
    hdrs = ["translate/mlir_roundtrip_flags.h"],
    deps = [
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
    ],
)

cc_library(
    name = "convert_type",
    srcs = ["utils/convert_type.cc"],
    hdrs = ["utils/convert_type.h"],
    deps = [
        ":tensorflow",
        ":tensorflow_dialect_registration",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Support",
    ],
)

cc_library(
    name = "convert_tensor",
    srcs = ["utils/convert_tensor.cc"],
    hdrs = ["utils/convert_tensor.h"],
    deps = [
        ":convert_type",
        ":mangling_util",
        ":tensorflow",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
        "@local_config_mlir//:IR",
    ],
)

tf_cc_test(
    name = "convert_tensor_test",
    size = "small",
    srcs = ["utils/convert_tensor_test.cc"],
    deps = [
        ":convert_tensor",
        "//tensorflow/compiler/xla:test",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/stream_executor/lib",
        "@local_config_mlir//:IR",
    ],
)

cc_library(
    name = "mangling_util",
    srcs = ["utils/mangling_util.cc"],
    hdrs = ["utils/mangling_util.h"],
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "@com_google_absl//absl/strings",
    ],
)

cc_library(
    name = "error_util",
    srcs = ["utils/error_util.cc"],
    hdrs = ["utils/error_util.h"],
    deps = [
        "//tensorflow/core:lib",
        "//tensorflow/stream_executor/lib",
        "@llvm//:support",
        "@local_config_mlir//:IR",
    ],
)

cc_library(
    name = "tf_dialect_passes",
    srcs = [
        "transforms/constant_fold.cc",
        "transforms/decode_constant.cc",
        "transforms/dialect_hooks.cc",
    ],
    hdrs = [
        "transforms/constant_fold.h",
        "transforms/decode_constant.h",
    ],
    deps = [
        ":convert_tensor",
        ":eval_util",
        ":tensorflow",
        "//tensorflow/c:tf_status",
        "//tensorflow/c/eager:c_api",
        "//tensorflow/core:framework",
        "//tensorflow/stream_executor",
        "//tensorflow/stream_executor/lib",
        "@llvm//:support",
        "@local_config_mlir//:Analysis",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Pass",
        "@local_config_mlir//:Support",
    ],
    alwayslink = 1,
)

cc_library(
    name = "tf_dialect_lib",
    deps = [
        ":tensorflow_dialect_registration",
        ":tf_dialect_passes",
        "@local_config_mlir//:StandardDialectRegistration",
    ],
)

cc_library(
    name = "tf_graph_optimization_pass",
    srcs = ["transforms/tf_graph_optimization_pass.cc"],
    hdrs = ["transforms/tf_graph_optimization_pass.h"],
    deps = [
        ":convert_graphdef",
        ":mlir_roundtrip_flags",
        "//tensorflow/compiler/tf2xla:functionalize_control_flow_pass_registration",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:graph",
        "//tensorflow/core:lib",
        "//tensorflow/core:ops",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Pass",
    ],
    alwayslink = 1,
)

cc_library(
    name = "eval_util",
    srcs = ["utils/eval_util.cc"],
    hdrs = ["utils/eval_util.h"],
    deps = [
        ":convert_tensor",
        ":convert_type",
        ":export_tf_dialect_op",
        ":mangling_util",
        "//tensorflow/c/eager:c_api",
        "//tensorflow/c/eager:c_api_internal",
        "//tensorflow/compiler/xla:status_macros",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:core_cpu_lib",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Support",
    ],
)

cc_library(
    name = "translate_lib",
    srcs = [
        "translate/tf_mlir_translate.cc",
    ],
    hdrs = [
        "translate/tf_mlir_translate.h",
    ],
    deps = [
        ":convert_graphdef",
        ":error_util",
        ":import_utils",
        ":mangling_util",
        ":mlir_roundtrip_flags",
        "//tensorflow/core:lib_proto_parsing",
        "//tensorflow/core:ops",
        "//tensorflow/core:protos_all_proto_cc",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Parser",
        "@local_config_mlir//:Pass",
    ],
)

cc_library(
    name = "translate_cl_options",
    srcs = [
        "translate/tf_mlir_translate_cl.cc",
    ],
    hdrs = [
        "translate/tf_mlir_translate_cl.h",
    ],
    deps = [
        "@llvm//:support",
    ],
    alwayslink = 1,
)

cc_library(
    name = "translate_registration",
    srcs = [
        "translate/tf_mlir_translate_registration.cc",
    ],
    deps = [
        ":convert_graphdef",
        ":mlir_roundtrip_flags",
        ":translate_cl_options",
        ":translate_lib",
        "//tensorflow/core:protos_all_proto_cc",
        "//tensorflow/stream_executor/lib",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Translation",
    ],
    alwayslink = 1,
)

tf_cc_test(
    name = "error_util_test",
    srcs = ["utils/error_util_test.cc"],
    tags = ["no_rocm"],
    deps = [
        ":error_util",
        "//tensorflow/compiler/xla:test",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "@llvm//:support",
        "@local_config_mlir//:IR",
    ],
)

tf_native_cc_binary(
    name = "derived_attr_populator_gen",
    srcs = [
        "translate/derived_attr_populator_gen.cc",
    ],
    deps = [
        "@llvm//:support",
        "@llvm//:tablegen",
        "@local_config_mlir//:TableGen",
    ],
)

genrule(
    name = "derived_attr_populator_inc",
    srcs = [
        "@local_config_mlir//:include/mlir/IR/OpBase.td",
        "ir/tf_generated_ops.td",
        "ir/tf_op_base.td",
        "ir/tf_ops.td",
    ],
    outs = [
        "translate/derived_attr_populator.inc",
    ],
    cmd = ("$(location :derived_attr_populator_gen) " +
           "-I external/local_config_mlir/include " +
           "$(location //tensorflow/compiler/mlir/tensorflow:ir/tf_ops.td) " + " -o $@"),
    tools = [":derived_attr_populator_gen"],
)

filegroup(
    name = "tensorflow_optimize_td_files",
    srcs = [
        "transforms/optimize.td",
    ],
)

gentbl(
    name = "tensorflow_optimize_inc_gen",
    tbl_outs = [
        (
            "-gen-rewriters",
            "transforms/generated_optimize.inc",
        ),
    ],
    tblgen = "@local_config_mlir//:mlir-tblgen",
    td_file = "transforms/optimize.td",
    td_srcs = [
        ":tensorflow_ops_td_files",
        "@local_config_mlir//:StdOpsTdFiles",
    ],
)

cc_library(
    name = "compile_mlir_util",
    srcs = ["utils/compile_mlir_util.cc"],
    hdrs = ["utils/compile_mlir_util.h"],
    deps = [
        ":convert_type",
        ":error_util",
        "//tensorflow/compiler/mlir/xla:hlo",
        "//tensorflow/compiler/mlir/xla:mlir_hlo_to_hlo",
        "//tensorflow/compiler/mlir/xla:type_to_shape",
        "//tensorflow/compiler/mlir/xla:xla_legalize_tf",
        "//tensorflow/compiler/tf2xla:common",
        "//tensorflow/compiler/tf2xla:xla_compiler",
        "//tensorflow/core:framework",
        "//tensorflow/stream_executor/lib",
        "@com_google_absl//absl/types:span",
        "@llvm//:support",
        "@local_config_mlir//:IR",
        "@local_config_mlir//:Parser",
    ],
)

tf_cc_test(
    name = "compile_mlir_util_test",
    size = "small",
    srcs = ["utils/compile_mlir_util_test.cc"],
    deps = [
        ":compile_mlir_util",
        "//tensorflow/compiler/tf2xla:common",
        "//tensorflow/compiler/tf2xla:xla_compiler",
        "//tensorflow/compiler/xla:test",
        "//tensorflow/compiler/xla:xla_data_proto",
        "//tensorflow/compiler/xla/service:hlo",
        "//tensorflow/core:test_main",
        "//tensorflow/stream_executor/lib",
    ],
)

cc_library(
    name = "mlir_passthrough_op",
    srcs = ["ops/mlir_passthrough_op.cc"],
    visibility = [
        "//visibility:public",
    ],
    deps = [
        "//tensorflow/core:framework",
    ],
    alwayslink = 1,
)

tf_gen_op_wrapper_py(
    name = "gen_mlir_passthrough_op_py",
    out = "gen_mlir_passthrough_op.py",
    deps = [":mlir_passthrough_op"],
)

# Library to get rewrite patterns lowering within TensorFlow.
#
# This is a separate library so that external passes can link only this library
# without linking any of the other tensorflow passes.
cc_library(
    name = "lower_tf_lib",
    srcs = [
        "transforms/lower_tf.cc",
    ],
    hdrs = [
        "transforms/lower_tf.h",
    ],
    deps = [
        ":tensorflow",
        "@local_config_mlir//:IR",
    ],
    alwayslink = 1,
)
