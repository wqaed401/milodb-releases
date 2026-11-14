# Issue #1 test builds

Linux x86_64 builds of the 4.15.0 source, made with its own `build_client.py`
(PyInstaller, bundled Tk 8.6 and Xft like the official build) on Debian 13.

| File | Contents |
| --- | --- |
| `milodb-fix` | 4.15.0 plus `canvas-styled-text-destroy.patch` |
| `milodb-control` | unchanged 4.15.0, built the same way |
| `canvas-styled-text-destroy.patch` | the fix, applies to the 4.15.0 source with `patch -p1` |

The patch destroys the Text widget owned by `CanvasStyledText`, unbinds its callback
correctly and cancels `StyledText`'s pending idle callback.

## Test

Copy a build into your existing MiloDB directory (it needs the `.dat`, `.css` and
`milodb-manifest.ver` files there) and run it instead of `milodb`:

```sh
cp milodb-fix ~/MiloDB/milodb-fix
cd ~/MiloDB && ./milodb-fix
```

Use it normally for longer than the corruption usually takes to appear. To compare
leaks, count the application's X windows (rises steadily with `milodb-control`, stays
roughly constant with `milodb-fix`):

```sh
xwininfo -root -tree | grep -c '^ \+0x'
```

SHA-256:

```
63410b9d46f5a1e81b250754c59305be58dcd4728c390b2859f0245d8a04c13c  milodb-fix
85562c71efb9c8ae62b18ce250d190843f047e42a446fb7b4b12017131247c23  milodb-control
```
