---
title: Swift&RxSwift

layout:post

tags: Swift

---

## Swift
## RxSwift 
`RxSwift.Resources.total`不可用问题,粘贴到podfile中安装即可

```c
  post_install do |installer|
      installer.pods_project.targets.each do |target|
          if target.name == 'RxSwift'
              target.build_configurations.each do |config|
                  if config.name == 'Debug'
                      config.build_settings['OTHER_SWIFT_FLAGS'] ||= ['-D', 'TRACE_RESOURCES']
                  end
              end
          end
      end
  end
```