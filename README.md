<p align="center">
  <img src="artwork/gst.wasm.svg" width="100" height="100" align="center"/>
</p>

`gst.wasm` is a **GStreamer port to WebAssembly**. Its main goal is to implement the support of WebAssembly
in GStreamer (and its dependencies) and bring the changes upstream. This repository is a placeholder to
keep up-to-date information required on how to build, develop, and deploy GStreamer in a browser or in
a `node.js` runtime environment until all the information is finally merged into the corresponding
projects.

## The stack
We use the standard libraries and tools used on GStreamer. The gray boxes are the dependencies and the
green ones are new projects that will become part of mainstream GStreamer.

```mermaid
block-beta
  columns 3
  gst["gst-plugins-web"]:1
  space
  js["js-gstreamer-bindings"]:1
  browser["Browser/Node.js"]:3
  Emscripten:3
  GStreamer:3
  GLib Orc Cerbero
  libffi space meson
  style browser fill:#90e2f7,stroke:#4a7580;
  style gst fill:#cee741,stroke:#728024;
  style js fill:#cee741,stroke:#728024;
  style Emscripten fill:#90e2f7,stroke:#4a7580;
  style GStreamer fill:#e6e6e6,stroke:#808080;
  style GLib fill:#e6e6e6,stroke:#808080;
  style Orc fill:#e6e6e6,stroke:#808080;
  style Cerbero fill:#e6e6e6,stroke:#808080;
  style libffi fill:#e6e6e6,stroke:#808080;
  style meson fill:#e6e6e6,stroke:#808080;
```

## Dependencies
GStreamer has plenty of dependencies, especially at the plugin level, but our goal is focused on the
core ones, as shown in the gray boxes of the [above diagram](#the-stack). All dependencies use the
[git-upstream-workflow](https://github.com/fluendo/git-upstream-workflow ) meaning that all branches
will be committed upstream and all features are cumulative.

### GLib
We use the fork at [Fluendo](https://github.com/fluendo/glib) in the [gst.wasm](https://github.com/fluendo/glib/tree/wasm-vips-2.76.0) branch.

We forked the excellent work done by [kleisauke](https://github.com/kleisauke/glib) which is also a fork of [GNOME](https://github.com/GNOME/glib).

The [current status](https://github.com/GNOME/glib/compare/2.76.0...fluendo:wasm-vips-2.76.0) is grouped in the next cumulative branches:
<!-- START guw glib.toml markdown -->
* ⏳ `wasm-2.76.0-revert-gregex` [(Branch link)](https://github.com/kleisauke/glib/tree/wasm-2.76.0-revert-gregex)
* ⏳ `wasm-2.76.0-no-g_error` [(Branch link)](https://github.com/kleisauke/glib/tree/wasm-2.76.0-no-g_error)
* ⏳ `wasm-2.76.0-function-pointer` [(Branch link)](https://github.com/kleisauke/glib/tree/wasm-2.76.0-function-pointer)
* ⏳ `wasm-2.76.0-wasm-vs-emscripten` [(Branch link)](https://github.com/kleisauke/glib/tree/wasm-2.76.0-wasm-vs-emscripten)
* ⏳ `wasm-2.76.0-canvas-in-thread` [(Branch link)](https://github.com/kleisauke/glib/tree/wasm-2.76.0-canvas-in-thread)
* ⏳ `wasm-2.76.0-main-loop-support` [(Branch link)](https://github.com/kleisauke/glib/tree/wasm-2.76.0-main-loop-support)
<!-- END guw glib.toml markdown -->


### GStreamer
We use the fork at [Fluendo](https://github.com/fluendo/gstreamer) in the [gst.wasm](https://github.com/fluendo/gstreamer/tree/wasm-1.22) branch.

The [current status](https://github.com/fluendo/gstreamer/compare/1.22.2...fluendo:gstreamer:wasm-1.22) is grouped in the next cumulative branches:
<!-- START guw gstreamer.toml markdown -->
* ⏳ `wasm-1.22-to-split`: First successful build with support for wasm [(Branch link)](https://github.com/fluendo/gstreamer/tree/wasm-1.22-to-split)
* ⏳ `wasm-1.22-test`: Enable unit tests for wasm [(Branch link)](https://github.com/fluendo/gstreamer/tree/wasm-1.22-test)
* ⏳ `wasm-1.22-wip`: Commits in progress [(Branch link)](https://github.com/fluendo/gstreamer/tree/wasm-1.22-wip)
* ⏳ `wasm-1.22-function-pointer`: Fix null function or function signature mismatch runtime errors [(Branch link)](https://github.com/fluendo/gstreamer/tree/wasm-1.22-function-pointer)
* ⏳ `wasm-1.22-openal`: Add emscripten support OpenAL to play audio [(Branch link)](https://github.com/fluendo/gstreamer/tree/wasm-1.22-openal)
<!-- END guw gstreamer.toml markdown -->

> [!NOTE]
> Rebase with main is in process.

### Cerbero
We use the fork at [Fluendo](https://github.com/fluendo/cerbero) in the [gst.wasm](https://github.com/fluendo/cerbero/tree/gst.wasm) branch.

The [current status](https://github.com/fluendo/cerbero/compare/main...fluendo:cerbero:gst.wasm) is grouped in the next cumulative branches:
<!-- START guw cerbero.toml markdown -->
* 🟢 `tests`: Bring back tests [(PR link)](https://gitlab.freedesktop.org/gstreamer/cerbero/-/merge_requests/1471)
* 🔄 `tests2`: Update more tests. Second round [(PR link)](https://gitlab.freedesktop.org/gstreamer/cerbero/-/merge_requests/1477)
* ⏳ `tests3`: Tests update final round [(PR link)](https://gitlab.freedesktop.org/gstreamer/cerbero/-/merge_requests/1482)
* ⏳ `local_source`: New type of source that allows you to build a recipe without fetching or copying sources [(PR link)](https://gitlab.freedesktop.org/gstreamer/cerbero/-/merge_requests/1483)
* ⏳ `packaging`: Enable installation using pip with a git-https repository [(PR link)](https://gitlab.freedesktop.org/gstreamer/cerbero/-/merge_requests/1484)
<!-- END guw cerbero.toml markdown -->


## Usage

### Samples

![videotestsrc ball pattern](docs/img/videotestsrc-sample.jpg)


#### Prepare environment

Use cerbero environmenet (TODO: cerbero environment is in progress)

#### Compile samples

```
cd samples
meson --cross-file=emscripten-crossfile.meson builddir
```

#### Running the samples
```
emrun builddir/videotestsrc/videotestsrc-example.html
emrun builddir/audiotestsrc/index.html # Press the 'play' button to hear to a sound.
emrun builddir/openal/openal-example.html # Click to hear to a sound.
```

## Development
