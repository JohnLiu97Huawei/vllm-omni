# Abstract

This document provides comprehensive instructions for installing and configuring Mooncake on ARM64 (aarch64) architecture systems.

## System Requirements

- **Operating System**: Ubuntu/Debian-based Linux
- **Architecture**: ARM64 (aarch6)  
- **Dependencies**: Ascend Toolkit (Huawei Ascend)

## Complete Installation Script

```Shell
# 1. Clone Mooncake repository
git clone https://github.com/kvcache-ai/Mooncake.git && \
cd /tmp/Mooncake && \

# 2. Set compilation environment variables
export CPATH=/usr/lib/aarch64-linux-gnu/mpich/include/:/usr/lib/aarch64-linux-gnu/openmpi/lib:$CPATH && \

# 3. Create symbolic links for glog library
mkdir -p /usr/lib/aarch64-linux-gnu/ && \
ln -s /usr/lib64/libglog.so /usr/lib/aarch64-linux-gnu/ && \
ln -s /usr/lib64/libglog.so.1 /usr/lib/aarch64-linux-gnu/ && \

# 4. Modify configuration files for Ascend support
sed -i '/option(USE_ASCEND_DIRECT/s/OFF/ON/' mooncake-common/common.cmake && \

# 5. Comment out apt-get commands in dependencies script
sed -i 's/^apt-get/#&/' dependencies.sh && \

# 6. Install dependencies (continue on error)
bash dependencies.sh --yes || true && \

# 7. Configure and build Mooncake
mkdir build && \
cd build && \
cmake -DUSE_ASCEND_DIRECT=ON -DBUILD_SHARED_LIBS=ON -DBUILD_UNIT_TESTS=OFF .. && \
make -j$(nproc) && \

# 8. Install the built libraries
make install && \

# 9. Copy shared libraries to Ascend Toolkit directory
cp mooncake-common/src/libmooncake_common.so \
   mooncake-transfer-engine/src/libtransfer_engine.so \
   mooncake-store/src/libmooncake_store.so \
   /usr/local/Ascend/ascend-toolkit/latest/python/site-packages/mooncake/ && \

# 10. Clean up temporary files
rm -rf /tmp/*
```
