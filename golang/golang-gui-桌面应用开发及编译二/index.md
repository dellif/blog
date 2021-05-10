
# 环境

- 基于 [上一篇](/golang/golang-gui-桌面应用开发及编译一/) 基础


# 代码事例

## 安装

```
go get github.com/lxn/walk
```

## 代码

```
package main
 
import (
    "github.com/lxn/walk"
    . "github.com/lxn/walk/declarative"
    "strings"
)
 
func main() {
    var inTE, outTE *walk.TextEdit
 
    MainWindow{
        Title:   "SCREAMO",
        MinSize: Size{600, 400},
        Layout:  VBox{},
        Children: []Widget{
            HSplitter{
                Children: []Widget{
                    TextEdit{AssignTo: &inTE},
                    TextEdit{AssignTo: &outTE, ReadOnly: true},
                },
            },
            PushButton{
                Text: "SCREAM",
                OnClicked: func() {
                    outTE.SetText(strings.ToUpper(inTE.Text()))
                },
            },
        },
    }.Run()
}
```

## 安装依赖

- rsrc 工具

> 码写好后，我们直接 go build 打包是不行的，golang 的图形 exe 需要依赖于 manifest 才能正常运行。而 go 却没有提供资源打包的所有功能，所以要把 manifest 嵌入 exe 文件中，还需要一个工具：rsrc。

```
go get github.com/akavel/rsrc
```
==使用 rsrc 需要带上路径，我本机路径是`$GOPATH/bin/rsrc`==

## 创建一个 manifest 文件, main.manifest

> 什么是 manifest

- https://docs.microsoft.com/zh-cn/windows/win32/sbscs/manifests?redirectedfrom=MSDN

> 介绍：Manifests are XML files that accompany and describe side-by-side assemblies or isolated applications. Manifests uniquely identify the assembly through the assembly’s assemblyIdentity element. They contain information used for binding and activation, such as COM classes, interfaces, and type libraries, that has traditionally been stored in the registry. Manifests also specify the files that make up the assembly and may include Windows classes if the assembly author wants them to be versioned. Side-by-side assemblies are not registered on the system, but are available to applications and other assemblies on the system that specify dependencies in manifest files.

是一种xml文件，标明所依赖的side-by-side组建。

如果用VS开发，可以Set通过porperty->configuration properties->linker->manifest file->Generate manifest To Yes来自动创建manifest来指定系统的和CRT的assembly版本。

详解
观察上面的manifest文件：

<xml>这是xml声明：

```
版本号----<?xml version="1.0"?>。
这是必选项。 尽管以后的 XML 版本可能会更改该数字，但是 1.0 是当前的版本。

编码声明------<?xml version="1.0" encoding="UTF-8"?>
这是可选项。 如果使用编码声明，必须紧接在 XML 声明的版本信息之后，并且必须包含代表现有字符编码的值。

standalone表示该xml是不是独立的，如果是yes，则表示这个XML文档时独立的，不能引用外部的DTD规范文件；如果是no，则该XML文档不是独立的，表示可以用外部的DTD规范文档。
```

<dependency>这一部分指明了其依赖于一个库：

```
<assemblyIdentity>属性里面还分别是：
type----系统类型，
version----版本号，
processorArchitecture----平台环境，
publicKeyToken----公匙
```

> 我们的示例

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0" xmlns:asmv3="urn:schemas-microsoft-com:asm.v3">
    <assemblyIdentity version="1.0.0.0" processorArchitecture="*" name="SomeFunkyNameHere" type="win32"/>
    <dependency>
        <dependentAssembly>
            <assemblyIdentity type="win32" name="Microsoft.Windows.Common-Controls" version="6.0.0.0" processorArchitecture="*" publicKeyToken="6595b64144ccf1df" language="*"/>
        </dependentAssembly>
    </dependency>
    <asmv3:application>
        <asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
            <dpiAware>true</dpiAware>
        </asmv3:windowsSettings>
    </asmv3:application>
</assembly>
```

# 生成 rsrc.syso

```
$GOPATH/bin/rsrc -manifest main.manifest -o rsrc.syso
```

# 打包成 exe 文件

```
dongge@dongge-PC:/data/webroot/localgo.test$ go build
main.go:6:2: build constraints exclude all Go files in /home/dongge/golang/pkg/mod/github.com/lxn/walk@v0.0.0-20191128110447-55ccb3a9f5c1
dongge@dongge-PC:/data/webroot/localgo.test$ CGO_EBABLED=1 go build
main.go:6:2: build constraints exclude all Go files in /home/dongge/golang/pkg/mod/github.com/lxn/walk@v0.0.0-20191128110447-55ccb3a9f5c1
dongge@dongge-PC:/data/webroot/localgo.test$ CGO_EBABLED=1 GOOS=windows go build
dongge@dongge-PC:/data/webroot/localgo.test$ CGO_EBABLED=1 GOOS=windows go build -ldflags="-H windowsgui"

```

==`go build` 虽然会生成 exe 文件，但会带着 cmd 命令窗口，通过加上参数 `go build -ldflags="-H windowsgui"`去除命令窗口==

# 打包图标

```
rsrc -manifest test.manifest –ico icon.ico -o rsrc.syso
```