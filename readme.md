# Issue description

When debugging a file with a sourceMappingURL like `//# sourceMappingURL=./file.es5.js.map`, everything works fine.
When debbuging a file with a sourceMappingURL like `//# sourceMappingURL=http://127.0.0.1:8567/file.es5.js.map`, vscode gets lost and you can barely remove the debug toolbar.

# How to reproduce

- Clone this repo: `git clone git@github.com:dmail/vscode-debug-sourcemap-http.git`
- Open `fetch-http/index.js` in vs code
- Launch it using `Launch with node` configuration

Here vscode debugging experience is broken.

## Additional information

- `//# sourceMappingURL=./file.es5.js.map` loads sourceMap using https://github.com/Microsoft/vscode-chrome-debug-core/blob/fb7ce14702c835253b6e0bad26fb746a2ce0f5d3/src/sourceMaps/sourceMapFactory.ts#L122;
- `//# sourceMappingURL=http://127.0.0.1:8567/file.es5.js.map` loads sourceMap using https://github.com/Microsoft/vscode-chrome-debug-core/blob/fb7ce14702c835253b6e0bad26fb746a2ce0f5d3/src/sourceMaps/sourceMapFactory.ts#L158

### `fetch-filesystem/index.js` logs

https://github.com/dmail/vscode-debug-sourcemap-http/blob/master/fetch-filesystem/debugadaptater.txt#L226

Just after pausing the debugger, vscode creates the sourcemap.

### `fetch-http/index.js` logs

https://github.com/dmail/vscode-debug-sourcemap-http/blob/master/fetch-http/debugadaptater.txt#L286

After pausing the debugger, sourcemap are never created.

## Possible explanation: Race condition

Fetching from filesystem is faster than fetching from http.
With `//# sourceMappingURL=./file.es5.js.map`, when vscode pause the code on `debugger` it has already read the file and created the sourceMap.
However with `//# sourceMappingURL=http://127.0.0.1:8567/file.es5.js.map`, when vscode pause the code on `debugger` it has not sourcemap and gets lost.