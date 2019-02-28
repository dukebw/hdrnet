TF_CFLAGS=-I/home/modiml/anaconda3/envs/hdrnet/lib/python3.7/site-packages/tensorflow/include -D_GLIBCXX_USE_CXX11_ABI=1
TF_LFLAGS=-L/home/modiml/anaconda3/envs/hdrnet/lib/python3.7/site-packages/tensorflow -ltensorflow_framework
CUDA_HOME ?= /usr/local/cuda

SRC_DIR = ops

BUILD_DIR = build
LIB_DIR = lib

CC = c++ -std=c++11
NVCC = nvcc -std c++11
CFLAGS = -fPIC $(TF_CFLAGS) -O2
LDFLAGS = -L$(CUDA_HOME)/lib64 -lcudart $(TF_LFLAGS)
NVFLAGS = -DGOOGLE_CUDA=1 -x cu -Xcompiler -fPIC $(TF_CFLAGS) -I$(SRC_DIR) --expt-relaxed-constexpr -Wno-deprecated-gpu-targets -ftz=true --ptxas-options=-v -lineinfo

SRC = bilateral_slice.cc
CUDA_SRC = bilateral_slice.cu.cc
CUDA_OBJ = $(addprefix $(BUILD_DIR)/,$(CUDA_SRC:.cc=.o))
SRCS = $(addprefix $(SRC_DIR)/, $(SRC))

all: $(LIB_DIR)/hdrnet_ops.so

# Main library
$(LIB_DIR)/hdrnet_ops.so: $(CUDA_OBJ) $(LIB_DIR) $(SRCS)
	$(CC) -shared -o $@ $(SRCS) $(CUDA_OBJ) $(CFLAGS) $(LDFLAGS) 

# Cuda kernels
$(BUILD_DIR)/%.o: $(SRC_DIR)/%.cc $(BUILD_DIR)
	@echo $(TF_LFLAGS)
	@echo $(TF_CFLAGS)
	$(NVCC) -c  $< -o $@ $(NVFLAGS)

$(BUILD_DIR):
	mkdir -p $@


$(LIB_DIR):
	mkdir -p $@

clean:
	rm -rf $(BUILD_DIR) $(LIB_DIR)
