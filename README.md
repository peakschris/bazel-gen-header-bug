This repository demonstrates an issue with Bazel, present in Bazel 6 and 7.

When a rule copies or generates a header file, supplies that copied header as a directory, and that header file is added as a srcs to cc_library (et al), an undeclared inclusion error is generated.

To workaround the issue, move the copy/codegen rule to hdrs. This allows the compile to succeed, but has the undesirable affect of adding the copy/codegen to the public interface of the library.

### To reproduce

```
git clone https://github.com/peakschris/bazel-gen-header-bug.git
cd bazel-gen-header-bug
bazel build //b:libb
```

Output:
```
INFO: Analyzed target //b:libb (0 packages loaded, 0 targets configured).
ERROR: D:/workdir/bazel-bugs/gen-header/b/BUILD.bazel:16:11: Compiling b/file1.cxx failed: undeclared inclusion(s) in rule '//b:libb':
this rule is missing dependency declarations for the following files included by 'b/file1.cxx':
  'bazel-out/x64_windows-fastbuild/bin/b/copy/header1.h'
Target //b:libb failed to build
Use --verbose_failures to see the command lines of failed build steps.
INFO: Elapsed time: 1.484s, Critical Path: 0.34s
INFO: 2 processes: 2 internal.
ERROR: Build did NOT complete successfully
```