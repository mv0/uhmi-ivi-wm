# SPDX-License-Identifier: Apache-2.0
#
# Copyright (c) 2024  Panasonic Automotive Systems, Co., Ltd.
# Copyright @ 2025, Collabora, Ltd.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# - ilmCommon
# - ilmControl
# - -lpthread
# - jansson

project('uhmi-ivi-wm',
  'c', 'cpp',
  version: '1.0.0',
  default_options: [
    'warning_level=3',
    'c_std=c2x',
    'cpp_std=c++20'
  ],
  meson_version: '>= 1.0.0',
  license: 'MIT/Expat',
)

pkgconfig = import('pkgconfig')
cpp = meson.get_compiler('cpp')

grpcpp_reflection_dep = cpp.find_library('grpc++_reflection')
protoc = find_program('protoc')
grpc_cpp = find_program('grpc_cpp_plugin')


protoc_gen = generator(protoc, \
                       output : ['@BASENAME@.pb.cc', '@BASENAME@.pb.h'],
                       arguments : ['--proto_path=@CURRENT_SOURCE_DIR@/app',
                         '--cpp_out=@BUILD_DIR@',
                         '@INPUT@'])

generated_protoc_sources = protoc_gen.process('app/agl_shell.proto')

grpc_gen = generator(protoc, \
                     output : ['@BASENAME@.grpc.pb.cc', '@BASENAME@.grpc.pb.h'],
                     arguments : ['--proto_path=@CURRENT_SOURCE_DIR@/app',
                       '--grpc_out=@BUILD_DIR@',
                       '--plugin=protoc-gen-grpc=' + grpc_cpp.path(),
                       '@INPUT@'])

generated_grpc_sources = grpc_gen.process('app/agl_shell.proto')

grpc_deps = [
    dependency('protobuf'),
    dependency('grpc'),
    dependency('grpc++'),
    grpcpp_reflection_dep,
]

prefix_path = get_option('prefix')
binplugin_dir = join_paths(prefix_path, get_option('bindir'))

deps_uhmi_ivi_wm = [
    grpc_deps,
    dependency('ilmControl'),
    dependency('ilmCommon'),
    dependency('jansson')
]

srcs_uhmi_ivi_wm = [
    'app/main.c',
    'app/comm_parser.c',
    'app/comm_receiver.c',
    'app/ilm_control_wrapper.c',
    'app/AglShellGrpcClient.cpp',
    'app/AglShellGrpcClient.h',
    generated_protoc_sources,
    generated_grpc_sources,
]

exe = executable(
    'uhmi-ivi-wm',
    srcs_uhmi_ivi_wm,
    dependencies: deps_uhmi_ivi_wm,
    install_rpath: binplugin_dir,
    install: true
)
