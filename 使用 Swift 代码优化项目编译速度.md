

## 引言
软件的性能是评价一个软件质量的重要指标，尤其在今天这个时代，性能已成为大型项目不可或缺的考虑因素之一。对于用户量极大的软件，如网银系统、在线购物商城等，更是必须保证其高效稳定的性能。在这种背景下，优化项目的编译速度就显得尤为重要。本文将介绍如何使用 Swift 代码优化项目编译速度。

## 找出编译耗时过长的文件
要优化项目的编译速度，首先需要把耗时过长的文件找出来，然后进行重点优化。这里会用到 Xcode build 的两个OTHER_SWIFT_FLAGS：

- `-Xfrontend`：如果编译或类型检查时耗时多长，则在 Xcode 中输出警告。
- `-debug-time-function-bodies`：输出每个函数的编译时长。

添加这些 flag 的方法为：
1. 选中 Target
2. 选中 Build Settings
3. 搜索 “Other Swift Flags”
4. 添加 `-Xfrontend -debug-time-function-bodies`

基于这两个 flag，有 3 个方法可以找到耗时过长的文件：

### 方法一：使用克魔助手
克魔助手是一款专为苹果手机 iOS 应用开发设计的辅助工具，提供了丰富的性能监控功能，帮助开发者优化应用的性能。以下是使用克魔助手找到编译耗时过长的文件的步骤：

1. 下载克魔助手：用户可以前往克魔助手工具官网进行免费下载，该工具提供绿色软件版本，无需其他安装流程。下载后解压即可开始使用。下载地址是 https://www.keymob.com 。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7fecdd3010844804b16c3eb44fe6c75d.png)

2. 注册和登录：为了使用克魔助手工具，用户需要在电脑上安装并登录该工具。在登录后，用户可以继续其他操作流程。克魔开发助手提供了简单的登录码获取流程，确保用户能够方便地使用该工具。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d7d163d59c454ba0baf6b79cd1196da5.png)


3.选择文件管理界面并开始监听：双击克魔开发助手.exe 启动克魔助手后，点击右上角的登录按钮，输入邮箱后，没登录码的点击获取登录码，有的可以直接输入登录码，登录成功后，选择文件管理界面。然后自定义选择列，并点击 “开始监听” 来查看应用程序的性能情况。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b17c7a8efdc14be8a2feefc9df3820f7.png)

### 方法二：使用 Xcode 控制台
在 Xcode 的控制台中输入以下命令即可输出时间最久的前 10 个文件：

```
find . -type f -name '*.swift' -exec \
  sh -c "echo '{}' && swiftc -v -c '{}' 2>&1 | awk '/^.{10}[ ]+[0-9\.]+ms/{print \$0}' | sort -rn | head -10" \;
```

### 方法三：使用 Xcode 插件
使用 Xcode 插件 SwiftLint 可以自动输出编译耗时最长的文件。安装方法如下：

1. 打开 Terminal，输入以下命令：

```
brew install swiftlint
```

2. 在 Xcode 中打开 Preferences（快捷键 `Command + ,`），选择 Text Editing，然后选择 Code completion。
3. 在 Code completion 下面的 Fuzzy matching 一栏中，勾选 Show snippets using: 和 Swift。然后再选择 Edit All-in-One Snippet...。
4. 进入 Snippet 编辑页面，复制以下代码并保存：

```
// MARK: - Performance Metrics

// Prints the compile time of each function or method in this file.
// Add to the `OTHER_SWIFT_FLAGS` build setting for the target.
// -Xfrontend -debug-time-function-bodies
//
// For more information about how to use debug-time-function-bodies see:
// - https://pspdfkit.com/blog/2018/how-to-reduce-swift-compile-times/
// - https://github.com/apple/swift/pull/13132#issuecomment-312358040
// - WWDC 2018 session 404 Optimizing Swift build times
// - https://youtu.be/nJwVabxL5Gw?t=11m16s
//
// If you add this to a Swift file, it will print the compile time of each
// method in this file to the console on build.
//
// Usage:
// In Xcode 9.x and later:
// 1. Open your project
// 2. Go to the Build Phases tab of your app's target
// 3. Click the + button in the top left and select "New Run Script Phase"
// 4. Add the following code to the script area below the shell:
//      "${PODS_ROOT}/SwiftLint/swiftlint" --no-cache --config "${PODS_ROOT}/SwiftLint/.swiftlint.yml"
//      "${PROJECT_DIR}/Scripts/perf.swift"
//    Note: You may need to customise the paths to suit your project structure.
// 5. Drag the new run script phase to be just before the "Compile Swift Sources"
//    phase in the list.
// 6. Rebuild your project (Command-B)
// 7. The compile time for each function in each source file will now be logged
//    to the console, sorted by longest compile time first.

import Foundation

let numBitsInByte = 8

extension Double {
    func rounded(toPlaces places: Int) -> Double {
        let divisor = pow(10.0, Double(places))
        return (self * divisor).rounded() / divisor
    }
}

extension String {
    func padding(toLength length: Int, withPad padCharacter: Character) -> String {
        let padding = String(repeatElement(padCharacter, count: max(0, length - count)))
        return self + padding
    }
}

func readableTime(_ time: Double) -> String {
    if time < 0.001 {
        return "< 0.001ms"
    } else if time < 1 {
        return "\(time.rounded(toPlaces: 3))ms"
    } else {
        var timeStr = "\(time.rounded(toPlaces: 2))s"
        if time > 60 {
            let minutes = Int(time / 60)
            let seconds = time.truncatingRemainder(dividingBy: 60)
            timeStr = "\(minutes)m \(seconds.rounded(toPlaces: 2))s"
        }
        return timeStr.padding(toLength: 10, withPad: " ")
    }
}

func measure<A>(name: String, _ f: () -> A) -> A {
    let start = DispatchTime.now()
    let result = f()
    let end = DispatchTime.now()
    let time = Double(end.uptimeNanoseconds - start.uptimeNanoseconds) / Double(numBitsInByte * 1000000)
    print("\(readableTime(time)): \(name)")
    return result
}

```

5. 在 Xcode 的 Build Phases 中添加一个 Run Script，将以下代码复制到 Run Script 中：

```
"${PODS_ROOT}/SwiftLint/swiftlint" --no-cache --config "${PODS_ROOT}/SwiftLint/.swiftlint.yml"
"${SRCROOT}/Scripts/perf.swift"
```

## 总结
优化项目的编译速度是一个不断迭代的过程，需要不断地寻找和解决问题。本文介绍了如何使用 Swift 代码来找出编译耗时过长的文件，并介绍了三种方法：使用克魔助手、使用 Xcode 控制台和使用 Xcode 插件 SwiftLint。希望这些方法能够帮助读者优化项目的编译速度。

## 参考资料

- [克魔助手官网](https://www.keymob.com)
