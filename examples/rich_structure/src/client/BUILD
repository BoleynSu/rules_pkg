# Copyright 2021 The Bazel Authors. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

load("@rules_pkg//:mappings.bzl", "pkg_attributes", "pkg_filegroup", "pkg_files", "pkg_mklink")
load("@rules_pkg//:pkg.bzl", "pkg_tar")
load("//:foo_defs.bzl", "shared_object_path_selector")

cc_library(
    name = "foolib",
    srcs = [
        "foocore.cc",
    ],
    hdrs = [
        "foo.h",
    ],
)

cc_binary(
    name = "foo",
    srcs = [
        "foo.cc",
    ],
    # TODO(aiuto): How to we get the data to be in /usr/share/foo?
    # See #153 as a starting point for runfiles discussions.
    data = [
        "bar.rules",
        "baz.rules",
    ],
    deps = [
        ":foolib",
    ],
)

cc_binary(
    name = "fooctl",
    srcs = [
        "fooctl.cc",
    ],
)

pkg_files(
    name = "binary",
    srcs = [
        ":foo",
        ":fooctl",
    ],
    # Where it should be in the final package
    prefix = select(shared_object_path_selector) + "/bin",
    # Should this work?
    # runfiles_prefix = select(shared_object_path_selector) + "/bin/runfiles",
)

pkg_files(
    name = "runtime",
    srcs = [
        ":foolib",
    ],
    prefix = "usr/lib/foo",
)

pkg_tar(
    name = "man",
    srcs = [
        "foo.1",
    ],
    out = "foo.1.gz",
    extension = ".gz",
)

pkg_files(
    name = "manpages",
    srcs = [
        ":man",
    ],
    attributes = pkg_attributes(
        group = "man",
        mode = "0444",
        owner = "man",
    ),
    prefix = "man/man1",
    visibility = ["//visibility:public"],
)

pkg_mklink(
    name = "usr_bin",
    src = select(shared_object_path_selector) + "/bin/foo",
    dest = "usr/bin/foo",
)

pkg_filegroup(
    name = "arch",
    srcs = [
        ":binary",
        ":runtime",
        ":usr_bin",
    ],
    visibility = ["//visibility:public"],
)
