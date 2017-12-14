# vscode的cpp插件设置MinGW

```json
{
    "name": "Win32",
    "intelliSenseMode": "clang-x64",
    "includePath": [
        "${workspaceRoot}/src",
        "${workspaceRoot}/thirdparty/boost/include",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include/c++",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include/c++/mingw32",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include/c++/backward",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include",
        "C:/MinGW/include",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include-fixed"
    ],
    "defines": [
        "_DEBUG",
        "UNICODE",
        "__GNUC__=6",
        "__cdecl=__attribute__((__cdecl__))"
    ],
    "browse": {
        "path": [
            "${workspaceRoot}/src",
            "${workspaceRoot}/thirdparty/boost/include",
            "C:/MinGW/lib/gcc/mingw32/6.3.0/include",
            "C:/MinGW/lib/gcc/mingw32/6.3.0/include-fixed",
            "C:/MinGW/include/*"
        ],
        "limitSymbolsToIncludedHeaders": true,
        "databaseFilename": ""
    }
}
```
