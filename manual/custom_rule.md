
After the 2.2.1 release, xmake not only natively supports the construction of multi-language files, but also allows users to implement complex unknown file builds by custom building rules.

We can extend the build support for other files by pre-setting the file suffixes supported by the rules:

```lua
-- Define a build rule for a markdown file
rule("markdown")
    set_extensions(".md", ".markdown")
    on_build_file(function (target, sourcefile)
        os.cp(sourcefile, path.join(target:targetdir(), path.basename(sourcefile) .. ".html"))
    end)

target("test")
    set_kind("binary")

    -- Make the test target support the construction rules of the markdown file
    add_rules("markdown")

    -- Adding a markdown file to build
    add_files("src/*.md")
    add_files("src/*.markdown")
```

We can also specify some other scattered files to be processed as markdown rules:

```lua
target("test")
    -- ...
    add_files("src/test/*.md.in", {rule = "markdown"})
```

A target can be superimposed to apply multiple rules to more customize its own build behavior, and even support different build environments.

<p class="tip">
Rules specified by `add_files("*.md", {rule = "markdown"})`, with a higher priority than the rule set by `add_rules("markdown")`.
</p>

| Interface                                       | Description                                                    | Supported Versions |
| ----------------------------------------------- | --------------------------------------------                   | --------           |
| [rule](#rule)                                   | Defining Rules                                                 | >= 2.1.9           |
| [add_imports](#ruleadd_imports)                 | Pre-importing extension modules for all custom scripts         | >= 2.1.9           |
| [set_extensions](#ruleset_extensions)           | Set the file extension type supported by the rule              | >= 2.1.9           |
| [on_load](#ruleon_load)                         | Custom Load Script                                             | >= 2.2.1           |
| [on_link](#ruleon_link)                         | Custom Link Script                                             | >= 2.2.7           |
| [on_build](#ruleon_build)                       | Custom Compilation Script                                      | >= 2.1.9           |
| [on_clean](#ruleon_clean)                       | Custom Cleanup Script                                          | >= 2.1.9           |
| [on_package](#ruleon_package)                   | Custom Package Script                                          | >= 2.1.9           |
| [on_install](#ruleon_install)                   | Custom Installation Script                                     | >= 2.1.9           |
| [on_uninstall](#ruleon_uninstall)               | Custom Uninstall Script                                        | >= 2.1.9           |
| [on_build_file](#ruleon_build_file)             | Custom the build script to implement single file build         | >= 2.2.1           |
| [on_build_files](#ruleon_build_files)           | Custom Compilation Scripts for Multi-File Construction         | >= 2.2.1           |
| [before_load](#rulebefore_load)                 | Custom pre-load script                                         | >= 2.2.1           |
| [before_link](#rulebefore_link)                 | Custom pre-link script                                         | >= 2.2.7           |
| [before_build](#rulebefore_build)               | Custom pre-compilation script                                  | >= 2.2.1           |
| [before_clean](#rulebefore_clean)               | Custom the script before cleanup                               | >= 2.2.1           |
| [before_package](#rulebefore_package)           | Custom the script before packaging                             | >= 2.2.1           |
| [before_install](#rulebefore_install)           | Custom Pre-Installation Scripts                                | >= 2.2.1           |
| [before_uninstall](#rulebefore_uninstall)       | Custom the script before uninstalling                          | >= 2.2.1           |
| [before_build_file](#rulebefore_build_file)     | Custom pre-compilation scripts to implement single file builds | >= 2.2.1           |
| [before_build_files](#rulebefore_build_files)   | Custom pre-compilation scripts for multi-file build            | >= 2.2.1           |
| [after_load](#ruleafter_load)                   | Custom script after loading                                    | >= 2.2.1           |
| [after_link](#ruleafter_link)                   | Custom script after linking                                    | >= 2.2.7           |
| [after_build](#ruleafter_build)                 | Custom script after compilation                                | >= 2.2.1           |
| [after_clean](#ruleafter_clean)                 | Custom script after cleaning                                   | >= 2.2.1           |
| [after_package](#ruleafter_package)             | Custom script after packaging                                  | >= 2.2.1           |
| [after_install](#ruleafter_install)             | Custom script after installing                                 | >= 2.2.1           |
| [after_uninstall](#ruleafter_uninstall)         | Custom script after uninstalling                               | >= 2.2.1           |
| [after_build_file](#ruleafter_build_file)       | Custom script after compiling the single file                  | >= 2.2.1           |
| [after_build_files](#ruleafter_build_files)     | Custom script after compiling the multiple file                | >= 2.2.1           |
| [rule_end](#rule_end)                           | End Definition Rule                                            | >= 2.1.9           |

### Built-in rules

sinceAfter the 2.2.1 release, xmake provides some built-in rules to simplify the daily xmake.lua description and support for some common build environments.

| Rules | Description | Supported Versions |
| ----------------------------------------------- | -------------------------------------------- | -------- |
| [mode.debug](#mode-debug) | Debug Mode Compilation Rules | >= 2.2.1 |
| [mode.release](#mode-release) | Release Mode Compilation Rules | >= 2.2.1 |
| [mode.check](#mode-check) | Detection Mode Compilation Rules | >= 2.2.1 |
| [mode.profile](#mode-profile) | Performance Analysis Mode Compilation Rules | >= 2.2.1 |
| [mode.coverage](#mode-coverage) | Coverage Analysis Compilation Mode Rules | >= 2.2.1 |
| [qt.static](#qt-static) | Qt Static Library Compilation Rules | >= 2.2.1 |
| [qt.shared](#qt-shared) | Qt Dynamic Library Compilation Rules | >= 2.2.1 |
| [qt.console](#qt-console) | Qt Console Compilation Rules | >= 2.2.1 |
| [qt.application](#qt-application) | Qt Application Compilation Rules | >= 2.2.1 |
| [wdk.umdf.driver](#wdk-umdf-driver) | WDK Environment umdf Driver Compilation Rules | >= 2.2.1 |
[wdk.umdf.binary](#wdk-umdf-binary) | WDK Environment umdf Driver Application Compilation Rules | >= 2.2.1 |
| [wdk.kmdf.driver](#wdk-kmdf-driver) | WDK Environment kmdf Driver Compilation Rules | >= 2.2.1 |
[wdk.kmdf.binary](#wdk-kmdf-binary) | WDK Environment kmdf Driver Application Compilation Rules | >= 2.2.1 |
| [wdk.wdm.driver](#wdk-wdm-driver) | WDK Environment wdm Driver Compilation Rules | >= 2.2.1 |
[wdk.wdm.binary](#wdk-wdm-binary) | WDK Environment wdm Driver Application Compilation Rules | >= 2.2.1 |

#### mode.debug

Add the configuration rules for the debug compilation mode for the current project xmake.lua, for example:

```lua
add_rules("mode.debug")
```

Equivalent to:

```lua
-- the debug mode
if is_mode("debug") then

    -- enable the debug symbols
    set_symbols("debug")

    -- disable optimization
    set_optimize("none")
end
```

We can switch to this compilation mode by ``xmake f -m debug`.

#### mode.release

Add the configuration rules for the release compilation mode for the current project xmake.lua, for example:

```lua
add_rules("mode.release")
```

Equivalent to:

```lua
-- the release mode
if is_mode("release") then

    -- set the symbols visibility: hidden
    set_symbols("hidden")

    -- enable2017 optimization
    set_optimize("fastest")

    -- strip all symbols
    set_strip("all")
end
```

We can switch to this compilation mode by ``xmake f -m release`.

#### mode.check

Add the check compilation mode configuration rules for the current project xmake.lua, generally used for memory detection, for example:

```lua
add_rules("mode.check")
```

Equivalent to:

```lua
-- the check mode
if is_mode("check") then

    -- enable the debug symbols
    set_symbols("debug")

    -- disable optimization
    set_optimize("none")

    -- attempt to enable some checkers for pc
    add_cxflags("-fsanitize=address", "-ftrapv")
    add_mxflags("-fsanitize=address", "-ftrapv")
    add_ldflags("-fsanitize=address")
end
```

We can switch to this compilation mode by ``xmake f -m check`.

#### mode.profile

Add configuration rules for the profile compilation mode for the current project xmake.lua, which is generally used for performance analysis, for example:

```lua
add_rules("mode.profile")
```

Equivalent to:

```lua
-- the profile mode
if is_mode("profile") then

    -- enable the debug symbols
    set_symbols("debug")

    -- enable gprof
    add_cxflags("-pg")
    add_ldflags("-pg")
end
```

We can switch to this compilation mode by ``xmake f -m profile`.

#### mode.coverage

Add the configuration rules for the coverage compilation mode for the current project xmake.lua, which is generally used for coverage analysis, for example:

```lua
add_rules("mode.coverage")
```

Equivalent to:

```lua
-- the coverage mode
if is_mode("coverage") then
    add_cxflags("--coverage")
    add_mxflags("--coverage")
    add_ldflags("--coverage")
end
```

We can switch to this compilation mode by ``xmake f -m coverage`.

#### qt.static

A static library program used to compile and generate Qt environments:

```lua
target("qt_static_library")
    add_rules("qt.static")
    add_files("src/*.cpp")
    add_frameworks("QtNetwork", "QtGui")
```

#### qt.shared

Dynamic library program for compiling and generating Qt environment:

```lua
target("qt_shared_library")
    add_rules("qt.shared")
    add_files("src/*.cpp")
    add_frameworks("QtNetwork", "QtGui")
```

#### qt.console

A console program for compiling and generating a Qt environment:

```lua
target("qt_console")
    add_rules("qt.console")
    add_files("src/*.cpp")
```

#### qt.application

Used to compile ui applications that generate Qt environments.

Quick(qml) application:

```lua
target("qt_quickapp")
    add_rules("qt.application")
    add_files("src/*.cpp")
    add_files("src/qml.qrc")
    add_frameworks("QtQuick")
```

Qt Widgets (ui/moc) application:

```lua
-- add target
target("qt_widgetapp")
    add_rules("qt.application")
    add_files("src/*.cpp")
    add_files("src/mainwindow.ui")
    add_files("src/mainwindow.h") -- Add a meta header file with Q_OBJECT
    add_frameworks("QtWidgets")
```

For more descriptions of Qt, see: [#160](https://github.com/xmake-io/xmake/issues/160)



#### wdk.env.kmdf

Application of the compilation environment setting of kmdf under WDK, need to cooperate with: `wdk.[driver|binary|static|shared]` and other rules to use.

#### wdk.env.umdf

Application of the umdf compiler environment settings under WDK, you need to cooperate with: `wdk.[driver|binary|static|shared]` and other rules to use.

#### wdk.env.wdm

Application wdm compiler environment settings under WDK, need to cooperate with: `wdk.[driver|binary|static|shared]` and other rules to use.

#### wdk.driver

Compile and generate drivers based on the WDK environment under Windows. Currently, only the WDK10 environment is supported.

Note: need to cooperate: `wdk.env.[umdf|kmdf|wdm]`Environmental rules are used.

```lua
-- add target
target("echo")

    -- add rules
    add_rules("wdk.driver", "wdk.env.kmdf")

    -- add files
    add_files("driver/*.c")
    add_files("driver/*.inx")

    -- add includedirs
    add_includedirs("exe")
```

#### wdk.binary

Compile and generate executable programs based on WDK environment under Windows. Currently, only WDK10 environment is supported.

Note: It is necessary to cooperate with: environment rules such as `wdk.env.[umdf|kmdf|wdm]`.

```lua
-- add target
target("app")

    -- add rules
    add_rules("wdk.binary", "wdk.env.umdf")

    -- add files
    add_files("exe/*.cpp")
```

#### wdk.static

Compile and generate static library programs based on WDK environment under Windows. Currently, only WDK10 environment is supported.

Note: It is necessary to cooperate with: environment rules such as `wdk.env.[umdf|kmdf|wdm]`.

```lua
target("nonpnp")

    -- add rules
    add_rules("wdk.static", "wdk.env.kmdf")

    -- add flags for rule: wdk.tracewpp
    add_values("wdk.tracewpp.flags", "-func:TraceEvents(LEVEL,FLAGS,MSG,...)", "-func:Hexdump((LEVEL,FLAGS,MSG,...))")

    -- add files
    add_files("driver/*.c", {rule = "wdk.tracewpp"})
```

#### wdk.shared

Compile and generate dynamic library programs based on WDK environment under Windows. Currently, only WDK10 environment is supported.

Note: It is necessary to cooperate with: environment rules such as `wdk.env.[umdf|kmdf|wdm]`.

```lua
target("nonpnp")

    -- add rules
    add_rules("wdk.shared", "wdk.env.wdm")

    -- add flags for rule: wdk.tracewpp
    add_values("wdk.tracewpp.flags", "-func:TraceEvents(LEVEL,FLAGS,MSG,...)", "-func:Hexdump((LEVEL,FLAGS,MSG,...))")

    -- add files
    add_files("driver/*.c", {rule = "wdk.tracewpp"})
```

#### wdk.tracewpp

Used to enable tracewpp to preprocess source files:

```lua
target("nonpnp")

    -- add rules
    add_rules("wdk.driver", "wdk.env.kmdf")

    -- add flags for rule: wdk.tracewpp
    add_values("wdk.tracewpp.flags", "-func:TraceEvents(LEVEL,FLAGS,MSG,...)", "-func:Hexdump((LEVEL,FLAGS,MSG,...))")

    -- add files
    add_files("driver/*.c", {rule = "wdk.tracewpp"})
    add_files("driver/*.rc")
```

For more information on WDK rules, see: [#159](https://github.com/xmake-io/xmake/issues/159)

#### win.sdk.application

Compile and generate the winsdk application.

```lua
-- add rules
add_rules("mode.debug", "mode.release")

-- define target
target("usbview")

    -- windows application
    add_rules("win.sdk.application")

    -- add files
    add_files("*.c", "*.rc")
    add_files("xmlhelper.cpp", {rule = "win.sdk.dotnet"})
```

#### wdk.sdk.dotnet

Used to specify certain c++ source files to be compiled as c++.net.

```lua
add_files("xmlhelper.cpp", {rule = "win.sdk.dotnet"})
```

For more information on WDK rules, see: [#159](https://github.com/xmake-io/xmake/issues/159)

### rule

#### Defining rules

```lua
rule("markdown")
    set_extensions(".md", ".markdown")
    on_build_file(function (target, sourcefile, opt)
        os.cp(sourcefile, path.join(target:targetdir(), path.basename(sourcefile) .. ".html"))
    end)
```

### rule:add_imports

#### Pre-importing extension modules for all custom scripts

For usage and description, please see: [target:add_imports](#targetadd_imports), the usage is the same.

### rule:set_extensions

#### Setting the file extension type supported by the rule

Apply rules to files with these suffixes by setting the supported extension file types, for example:

```lua
-- Define a build rule for a markdown file
rule("markdown")
    set_extensions(".md", ".markdown")
    on_build_file(function (target, sourcefile, opt)
        os.cp(sourcefile, path.join(target:targetdir(), path.basename(sourcefile) .. ".html"))
    end)

target("test")
    set_kind("binary")

    -- Make the test target support the construction rules of the markdown file
    add_rules("markdown")

    -- Adding a markdown file to build
    add_files("src/*.md")
    add_files("src/*.markdown")
```

### rule:on_load

#### Custom load script

The load script used to implement the custom rules will be executed when the target is loaded. You can customize some target configurations in it, for example:

```lua
rule("test")
    on_load(function (target)
        target:add("defines", "-DTEST")
    end)
```

### rule:on_link

#### Custom link script

The link script used to implement the custom rules overrides the default link behavior of the applied target, for example:

```lua
rule("test")
    on_link(function (target)
    end)
```

### rule:on_build

#### Custom compilation script

The build script used to implement the custom rules overrides the default build behavior of the target being applied, for example:

```lua
rule("markdown")
    on_build(function (target)
    end)
```

### rule:on_clean

#### Custom cleanup script

The cleanup script used to implement the custom rules will override the default cleanup behavior of the applied target, for example:

```lua
rule("markdown")
    on_clean(function (target)
        -- remove sourcefile.html
    end)
```

### rule:on_package

#### Custom packaging script

A packaging script for implementing custom rules that overrides the default packaging behavior of the target being applied, for example:

```lua
rule("markdown")
    on_package(function (target)
        -- package sourcefile.html
    end)
```

### rule:on_install

#### Custom installation script

An installation script for implementing custom rules that overrides the default installation behavior of the target being applied, for example:

```lua
rule("markdown")
    on_install(function (target)
    end)
```

### rule:on_uninstall

#### Custom Uninstall Script

An uninstall script for implementing custom rules that overrides the default uninstall behavior of the target being applied, for example:

```lua
rule("markdown")
    on_uninstall(function (target)
    end)
```

### rule:on_build_file

#### Customizing the build script to process one source file at a time

```lua
rule("markdown")
    on_build_file(function (target, sourcefile, opt)
        print("%%%d: %s", opt.progress, sourcefile)
    end)
```

The third parameter opt is an optional parameter, which is used to obtain some information state during the compilation process. For example, opt.progress is the compilation progress of the current period.

### rule:on_build_files

#### Customizing the build script to process multiple source files at once

Most of the custom build rules, each time processing a single file, output a target file, for example: a.c => a.o

However, in some cases, we need to enter multiple source files together to build an object file, for example: a.c b.c d.c => x.o

For this situation, we can achieve this by customizing this script:

```lua
rule("markdown")
    on_build_files(function (target, sourcebatch, opt)
        -- build some source files
        for _, sourcefile in ipairs(sourcebatch.sourcefiles) do
            -- ...
        end
    end)
```

### rule:before_load

#### Custom pre-load script

Used to implement the execution script before the custom target is loaded, for example:

```lua
rule("test")
    before_load(function (target)
        target:add("defines", "-DTEST")
    end)
```

### rule:before_link

#### Custom pre-link script

Execution scripts used to implement custom target links, for example:

```lua
rule("test")
    before_link(function (target)
    end)
```

### rule:before_build

#### Custom pre-compilation script

Used to implement the execution script before the custom target is built, for example:

```lua
rule("markdown")
    before_build(function (target)
    end)
```

### rule:before_clean

#### Custom pre-cleanup script

Used to implement the execution script before the custom target cleanup, for example:

```lua
rule("markdown")
    before_clean(function (target)
    end)
```

### rule:before_package

#### Custom the pre-package script

Used to implement the execution script before the custom target is packaged, for example:

```lua
rule("markdown")
    before_package(function (target)
    end)
```

### rule:before_install

#### Custom pre-installation script

Used to implement the execution script before the custom target installation, for example:

```lua
rule("markdown")
    before_install(function (target)
    end)
```

### rule:before_uninstall

#### Custom pre-uninstall script

Used to implement the execution script before the custom target is uninstalled, for example:

```lua
rule("markdown")
    before_uninstall(function (target)
    end)
```

### rule:before_build_file

#### Custom pre-compilation script to process one source file at a time

Similar to [rule:on_build_file](#ruleon_build_file), but the timing of this interface is called before compiling a source file.
Generally used to preprocess some source files before compiling.

### rule:before_build_files

#### Custom pre-compilation script to process multiple source files at once

Similar to [rule:on_build_files](#ruleon_build_files), but the timing of this interface is called before compiling some source files.
Generally used to preprocess some source files before compiling.

### rule:after_load

#### Custom post-loading script

The execution script used to implement the custom target loading is similar to [rule:after_load](#ruleafter_load).

### rule:after_link

#### Custom post-linking script

The execution script used to implement the custom target link is similar to [rule:after_link](#ruleafter_link).

### rule:after_build

#### Custom post-compilation script

The execution script used to implement the custom target build is similar to [rule:before_build](#rulebefore_build).

### rule:after_clean

#### Custom post-cleaning script

The execution script used to implement the custom target cleanup is similar to [rule:before_clean](#rulebefore_clean).

### rule:after_package

#### Custom post-packaging script

The execution script used to implement the custom target package is similar to [rule:before_package](#rulebefore_package).

### rule:after_install

#### Custom post-installation script

The execution script used to implement the custom target installation is similar to [rule:before_install](#rulebefore_install).

### rule:after_uninstall

#### Custom post-uninstallation Script

The execution script used to implement the custom target uninstallation is similar to [rule:before_uninstall](#rulebefore_uninstall).

### rule:after_build_file

#### Custom post-compilation scripts to process one source file at a time

Similar to [rule:on_build_file](#ruleon_build_file), but the timing of this interface is called after compiling a source file.
Generally used to post-process some compiled object files.

### rule:after_build_files

#### Custom post-compilation scripts to process multiple source files at once

Similar to [rule:on_build_files](#ruleon_build_files), but the timing of this interface is called after compiling some source files.
Generally used to post-process some compiled object files.

### rule_end

#### End definition rules

This is optional. If you want to manually end the rule definition, you can call it:

```lua
rule("test")
    -- ..
rule_end()
```

