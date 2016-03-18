cmake_minimum_required(VERSION 3.1)

project(darknet)

option(CUDA "Build with CUDA GPU support" OFF)

if(GPU)
  find_package(CUDA REQUIRED)
  set(CUDA_NVCC_FLAGS -DGPU;--gpu-architecture=compute_20;--gpu-code=compute_20)
  cuda_add_library(darknet SHARED
    convolutional_kernels.cu src/deconvolutional_kernels.cu src/activation_kernels.cu src/im2col_kernels.cu src/col2im_kernels.cu src/blas_kernels.cu src/crop_layer_kernels.cu src/dropout_layer_kernels.cu src/maxpool_layer_kernels.cu src/softmax_layer_kernels.cu src/network_kernels.cu src/avgpool_layer_kernels.cu)
  message(STATUS "Found CUDA ${CUDA_VERSION}")

  target_compile_definitions(darknet PRIVATE -DGPU)
  target_link_libraries(darknet ${CUDA_cublas_LIBRARY})
  target_link_libraries(darknet ${CUDA_curand_LIBRARY})
else(GPU)
  add_library(darknet SHARED
    src/network.c)
endif(GPU)

target_sources(darknet
  PRIVATE
  src/gemm.c src/utils.c src/cuda.c src/deconvolutional_layer.c src/convolutional_layer.c src/list.c src/image.c src/activations.c src/im2col.c src/col2im.c src/blas.c src/crop_layer.c src/dropout_layer.c src/maxpool_layer.c src/softmax_layer.c src/data.c src/matrix.c src/network.c src/connected_layer.c src/cost_layer.c src/parser.c src/option_list.c src/darknet.c src/detection_layer.c src/imagenet.c src/captcha.c src/route_layer.c src/writing.c src/box.c src/nightmare.c src/normalization_layer.c src/avgpool_layer.c src/coco.c src/dice.c src/yolo.c src/layer.c src/compare.c src/classifier.c src/local_layer.c src/swag.c src/shortcut_layer.c src/activation_layer.c src/rnn_layer.c src/gru_layer.c src/rnn.c src/rnn_vid.c src/crnn_layer.c src/coco_demo.c src/tag.c src/cifar.c src/yolo_demo.c src/go.c src/batchnorm_layer.c src/art.c)
target_include_directories(darknet PRIVATE src)

install(TARGETS darknet
  LIBRARY DESTINATION lib)
