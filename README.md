# podfile-commit-lock
用于在App发布版本后打Tag前，将所有依赖都写在Podfile中，如果是指向git分支的锁定commit，以便今后Checkout Tag还可以pod install 通过

## 使用方法

### Podfile 区域

> 目前仅支持简单依赖替换，复杂的多target里的内容暂不支持

Podfile中将pod依赖区域使用以下注释包裹起来

```
### DEPENDENCIES AREA - START
<pod 依赖内容>
### DEPENDENCIES AREA - END
```

```
source 'https://github.com/CocoaPods/Specs.git'

platform :ios, '6.0'
inhibit_all_warnings!

xcodeproj 'MyProject'

### DEPENDENCIES AREA - START
pod 'AFNetworking', '1.0.0'
pod 'ObjectiveSugar', :git => '<url>', :branch => 'develop'
### DEPENDENCIES AREA - END

post_install do |installer|
  installer.pods_project.targets.each do |target|
    puts "#{target.name}"
  end
end
```

在其中的区域执行后会被替换成完整依赖内容

```
source 'https://github.com/CocoaPods/Specs.git'

platform :ios, '6.0'
inhibit_all_warnings!

xcodeproj 'MyProject'

### DEPENDENCIES AREA - START
pod 'AFNetworking', '1.0.0'
pod 'ObjectiveSugar', :git => '<url>', :branch => 'develop', :commit => 'blabla'
### DEPENDENCIES AREA - END

post_install do |installer|
  installer.pods_project.targets.each do |target|
    puts "#{target.name}"
  end
end
```

### 命令行参数

```
podfilecommitlock <projectPath>
```

例如

```
podfilecommitlock ./
```

指定目录下必须有Podfile文件和Podfile.lock文件
