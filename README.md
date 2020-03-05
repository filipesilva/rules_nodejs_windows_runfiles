# rules_nodejs_windows_runfiles

This is a repro for https://github.com/bazelbuild/rules_nodejs/issues/1689

Following https://github.com/bazelbuild/bazel/issues/5807, it is possible to use runfiles on Windows for machines that have symlinks enabled. 
The option that controls this is `--enable-runfiles`, defaulting to false on Windows and to true on other OSs.

When using runfiles, `bazel run` and `bazel test` should use a directory populated with these runfiles.
However, using `rules_nodejs` that doesn't happen.

## Repro

```
git clone https://github.com/filipesilva/rules_nodejs_windows_runfiles
cd rules_nodejs_windows_runfiles
npm install
npm run example
```

In Linux, you will see this output:
```
file path is /home/filipesilva/.cache/bazel/_bazel_filipesilva/8aae2c1c8f8783e029b7d6963ffe91c3/execroot/rules_nodejs_windows_runfiles/bazel-out/k8-fastbuild/bin/example.sh.runfiles/rules_nodejs_windows_runfiles/main.js
```

You can tell the runfiles directory is user because the path contains `example.sh.runfiles`.

On Windows, you will see this output:
```
file path is D:\sandbox\rules_nodejs_windows_runfiles\main.js
```

This is the wrong path. 
Instead, it should have been `C:\users\kamik\_bazel_kamik\xv6tfi25\execroot\rules_nodejs_windows_runfiles\bazel-out\x64_windows-fastbuild\bin\example.bat.runfiles\rules_nodejs_windows_runfiles\main.js` (note the `example.bat.runfiles` part).

This is an important distinction given that on Windows data files are present only in their source location when not using runfiles.
Attempting to resolve output files relative to data files on Windows is thus not possible without runfiles.