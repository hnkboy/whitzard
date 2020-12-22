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


##############################################################################
# vppctl binary
##############################################################################
add_whitzard_executable(vppctl
  SOURCES app/vppctl.c
  LINK_LIBRARIES vppinfra
)

add_whitzard_executable(vpp
  ENABLE_EXPORTS
  SOURCES vnet/main.c
  LINK_LIBRARIES vlib vppinfra Threads::Threads ${CMAKE_DL_LIBS}
)
install(FILES conf/startup.conf DESTINATION etc/vpp COMPONENT vpp)
install(FILES conf/80-vpp.conf DESTINATION etc/sysctl.d COMPONENT vpp)
