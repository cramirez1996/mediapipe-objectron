package(
    default_visibility = ["//src:__subpackages__"],
)

genrule(
    name = "binary_graph",
    srcs = select({
        "//conditions:default": ["//mediapipe/graphs/object_detection_3d:mobile_gpu_binary_graph_shoe"],
        ":use_chair_model": ["//mediapipe/graphs/object_detection_3d:mobile_gpu_binary_graph_chair"],
    }),
    outs = ["objectdetection3d.binarypb"],
    cmd = "cp $< $@",
)

genrule(
    name = "model",
    srcs = select({
        "//conditions:default": ["//mediapipe/models:object_detection_3d_sneakers.tflite"],
        ":use_chair_model": ["//mediapipe/models:object_detection_3d_chair.tflite"],
    }),
    outs = ["object_detection_3d.tflite"],
    cmd = "cp $< $@",
)

cc_binary(
    name = "libmediapipe_jni.so",
    linkshared = 1,
    linkstatic = 1,
    deps = [
        "//mediapipe/graphs/object_detection_3d:mobile_calculators",
        "//mediapipe/java/com/google/mediapipe/framework/jni:mediapipe_framework_jni",
    ],
)

cc_library(
    name = "mediapipe_jni_lib",
    srcs = [":libmediapipe_jni.so"],
    alwayslink = 1,
)

# To use the "chair" model instead of the default "shoes" model,
# add "--define chair=true" to the bazel build command.
config_setting(
    name = "use_chair_model",
    define_values = {
        "chair": "true",
    },
)

android_library(
    name = "greeter_activity",
    srcs = [
        "Greeter.java",
        "MainActivity.java",
    ],
    manifest = "AndroidManifest.xml",
    resource_files = glob(["res/**"]),
    assets = [
        ":binary_graph",
        ":model",
        "//src/main/java/com/example/bazel/assets:box.obj.uuu",
        "//src/main/java/com/example/bazel/assets:classic_colors.png",
    ] + select({
        "//conditions:default": [
            "//src/main/java/com/example/bazel/assets/sneaker:model.obj.uuu",
            "//src/main/java/com/example/bazel/assets/sneaker:texture.bmp",
        ],
        ":use_chair_model": [
            "//src/main/java/com/example/bazel/assets/chair:model.obj.uuu",
            "//src/main/java/com/example/bazel/assets/chair:texture.bmp",
        ],
    }),
    assets_dir = "",
    deps = [
        ":mediapipe_jni_lib",
        "//mediapipe/framework/formats:landmark_java_proto_lite",
        "//mediapipe/java/com/google/mediapipe/components:android_camerax_helper",
        "//mediapipe/java/com/google/mediapipe/components:android_components",
        "//mediapipe/java/com/google/mediapipe/framework:android_framework",
        "//mediapipe/java/com/google/mediapipe/glutil",
        "//third_party:androidx_appcompat",
        "//third_party:androidx_constraint_layout",
        "//third_party:androidx_legacy_support_v4",
        "//third_party:androidx_recyclerview",
        "//third_party:opencv",
        "@androidx_concurrent_futures//jar",
        "@androidx_lifecycle//jar",
        "@com_google_code_findbugs//jar",
        "@com_google_guava_android//jar",
        "@maven//:com_google_ar_core",
        "@maven//:com_google_ar_sceneform_core",
        "@maven//:com_google_ar_sceneform_ux_sceneform_ux"
        ],
)

