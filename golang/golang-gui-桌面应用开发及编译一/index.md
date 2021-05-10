
> 以下参考 https://www.jianshu.com/p/456f072f58af，编译过程多花了点时间

# 准备

- deepin 15.11
- golang 1.14.3
- GTK+3.10 以上
- andlabs/ui

```
#查看 GTK 版本
dongge@dongge-PC:/media/dongge/D/data/webroot/localgo.test$ pkg-config --list-all |grep gtk
gtk+-3.0                       GTK+ - GTK+ Graphical UI Library
gtk+-unix-print-3.0            GTK+ - GTK+ Unix print support
gtk+-wayland-3.0               GTK+ - GTK+ Graphical UI Library
gtk+-broadway-3.0              GTK+ - GTK+ Graphical UI Library
gtk+-x11-3.0                   GTK+ - GTK+ Graphical UI Library

```

- 安装依赖
 
```
sudo apt-get install libgtk-3-dev
sudo apt-get install gcc-multilib
sudo apt-get install gcc-mingw-w64
```

# 开发事例

## 安装

```
go get github.com/andlabs/libui
go get github.com/andlabs/ui
```

## 代码实例

```
package main

import (
	"github.com/andlabs/ui"
	_ "github.com/andlabs/ui/winmanifest"
)

func main() {
	err := ui.Main(func() {
		// 生成：文本框
		name := ui.NewEntry()
		// 生成：标签
		greeting := ui.NewLabel(``)
		// 生成：按钮
		button := ui.NewButton(`欢迎`)
		// 设置：按钮点击事件
		button.OnClicked(func(*ui.Button) {
			greeting.SetText(`你好，` + name.Text() + `！`)
		})
		// 生成：垂直容器
		box := ui.NewVerticalBox()

		// 往 垂直容器 中添加 控件
		box.Append(ui.NewLabel(`请输入你的名字：`), false)
		box.Append(name, false)
		box.Append(button, false)
		box.Append(greeting, false)

		// 生成：窗口（标题，宽度，高度，是否有 菜单 控件）
		window := ui.NewWindow(`你好`, 200, 100, false)

		// 窗口容器绑定
		window.SetChild(box)

		// 设置：窗口关闭时
		window.OnClosing(func(*ui.Window) bool {
			// 窗体关闭
			ui.Quit()
			return true
		})

		// 窗体显示
		window.Show()
	})
	if err != nil {
		panic(err)
	}
}

```

# 编译尝试过程

```
dongge@dongge-PC:/data/webroot/localgo.test$ CGO_ENABLED=0 GOOS=windows GOARCH=386 go build main.go
main.go:4:2: build constraints exclude all Go files in /home/dongge/golang/pkg/mod/github.com/andlabs/ui@v0.0.0-20180902183112-867a9e5a498d
dongge@dongge-PC:/data/webroot/localgo.test$ CGO_ENABLED=1 GOOS=windows GOARCH=386 go build main.go
# runtime/cgo
gcc: error: unrecognized command line option ‘-mthreads’; did you mean ‘-pthread’?
dongge@dongge-PC:/data/webroot/localgo.test$ GOOS=windows GOARCH=386 CGO_ENABLED=1 CXX_FOR_TARGET=i686-w64-mingw32-g++ CC_FOR_TARGET=i686-w64-mingw32-gcc go build main.go 
# runtime/cgo
gcc: error: unrecognized command line option ‘-mthreads’; did you mean ‘-pthread’?
dongge@dongge-PC:/data/webroot/localgo.test$ CGO_ENABLED=1 CC=x86_64-w64-mingw32-gcc CXX=x86_64-w64-mingw32-g++ GOOS=windows GOARCH=amd64 go build -o main.exe main.go
# 已成功
```

==在 windows 上运行正常==