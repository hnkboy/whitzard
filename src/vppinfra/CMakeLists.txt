# Copyright (c) 2018 Cisco and/or its affiliates.
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at:
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

enable_language(ASM)

##############################################################################
# Generate vppinfra/config.h
##############################################################################
set(LOG2_CACHE_LINE_BYTES ${VPP_LOG2_CACHE_LINE_SIZE})

option(VPP_VECTOR_GROW_BY_ONE "Vectors grow by one, instead of 3/2" OFF)
if(VPP_VECTOR_GROW_BY_ONE)
  set(VECTOR_GROW_BY_ONE 1)
else(VPP_VECTOR_GROW_BY_ONE)
  set(VECTOR_GROW_BY_ONE 0)
endif(VPP_VECTOR_GROW_BY_ONE)

configure_file(
  ${CMAKE_SOURCE_DIR}/src/vppinfra/config.h.in
  ${CMAKE_BINARY_DIR}/src/vppinfra/config.h
)

install(
  FILES ${CMAKE_BINARY_DIR}/vppinfra/config.h
  DESTINATION include/vppinfra
  COMPONENT vpp-dev
)

add_definitions(-fvisibility=hidden)

##############################################################################
# vppinfra sources
##############################################################################
set(VPPINFRA_SRCS
  bihash_all_vector.c
)

set(VPPINFRA_HEADERS
  sanitizer.h
  bihash_16_8.h
  bihash_24_8.h
  bihash_40_8.h
  bihash_48_8.h
  bihash_8_8.h
  bihash_8_16.h
  bihash_24_16.h
  bihash_template.c
  bihash_template.h
  bihash_vec8_8.h
  bitmap.h
  bitops.h
  byte_order.h
  cache.h
  callback.h
  callback_data.h
  clib_error.h
  clib.h
  cpu.h
  crc32.h
  dlist.h
  dlmalloc.h
  elf_clib.h
  elf.h
  elog.h
  error_bootstrap.h
  error.h
  fifo.h
  file.h
  format.h
  graph.h
  hash.h
  heap.h
  lb_hash_hash.h
  llist.h
  lock.h
  longjmp.h
  macros.h
  maplog.h
  math.h
  memcpy_avx2.h
  memcpy_avx512.h
  memcpy_sse3.h
  mem.h
  mhash.h
  mpcap.h
  os.h
  pcap.h
  pcap_funcs.h
  pmalloc.h
  pool.h
  pmc.h
  ptclosure.h
  random_buffer.h
  random.h
  random_isaac.h
  rbtree.h
  serialize.h
  sha2.h
  smp.h
  socket.h
  sparse_vec.h
  string.h
  time.h
  time_range.h
  timing_wheel.h
  tw_timer_2t_2w_512sl.h
  tw_timer_16t_1w_2048sl.h
  tw_timer_16t_2w_512sl.h
  tw_timer_1t_3w_1024sl_ov.h
  tw_timer_2t_1w_2048sl.h
  tw_timer_4t_3w_256sl.h
  tw_timer_template.c
  tw_timer_template.h
  types.h
  atomics.h
  unix.h
  valloc.h
  vec_bootstrap.h
  vec.h
  vector_altivec.h
  vector_avx2.h
  vector_avx512.h
  vector_funcs.h
  vector.h
  vector_neon.h
  vector_sse42.h
  warnings.h
  xxhash.h
  linux/syscall.h
  linux/sysfs.h
)

if("${CMAKE_SYSTEM_NAME}" STREQUAL "Linux")
  list(APPEND VPPINFRA_SRCS
    elf_clib.c
    linux/mem.c
    linux/sysfs.c
   )
endif()

add_whitzard_library(vppinfra
  SOURCES ${VPPINFRA_SRCS}
  LINK_LIBRARIES m
  INSTALL_HEADERS ${VPPINFRA_HEADERS}
  COMPONENT libvppinfra
)
