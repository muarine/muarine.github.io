## Maven相关基础

### maven自带的插件都是以maven开头

| 自带的插件名                  | 应用                                       |
| ----------------------- | ---------------------------------------- |
| maven-antrun-plugin     | 配置以Ant的方式编写Target， 然后交给该插件的run目标去执行。在一些由Ant往Maven迁移的项目中，该插件尤其有用。此外当你发现需要编写一些自定义程度很高的任务，同时又觉 得Maven不够灵活时，也可以以Ant的方式实现 |
| maven-assembly-plugin   | 制作项目分发包，该分发包可能包含了项目的可执行文件、源代码、readme、平台脚本等。 支持各种主流的格式如zip、tar.gz、jar和war等， 使用一个名为assembly.xml的元数据文件来表述打包 |
| maven-checkstyle-plugin | 检查代码风格                                   |
| maven-compiler-plugin   | 编译源文件，可指定JDK版本                           |
| maven-dependency-plugin | 帮助分析依赖jar， dependency:list能够列出项目最终解析到的依赖列表，dependency:tree能进一步的描绘项目依赖树， dependency:analyze可以告诉你项目依赖潜在的问题，如： 有直接使用到的却未声明的依赖，该目标就会发出警告 |
| maven-enforcer-plugin   | maven-enforcer- plugin能够帮助你避免之类问题，它允许你创建一系列规则强制大家遵守，包括设定Java版本、设定Maven版本、禁止某些依赖、禁止 SNAPSHOT依赖 |
| maven-help-plugin       | help:system可以打印所有可用的环境变量和Java系统属性， help:effective-pom 打印项目的有效POM  help:effective-settings 打印有效settings |
| maven-resources-plugin  | 处理资源文件， 资源文件过滤                           |
| maven-surefire-plugin   | 跳过测试、排除某些 测试类、或者使用一些TestNG               |

