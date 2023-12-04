# Appium UiAutomator2 环境快速搭建指南
Appium 是一个比较流行的开源的跨平台自动化测试框架，环境搭建虽然比较简单，但当前互联网上的指导文档大多为简单的拷贝粘贴，并未实际操作过，直接按照 \
其指导搭建会遇到大量的问题严重浪费开发人员的时间，故整理了一份快速搭建文档，保障大家严格以下文档操作，能够一步成功，节省时间


# 目录
* 安装 Appium
* 安装 Appium 以及依赖 （这里使用 UiAutomator2 driver）
* 安装 Appium client & 写一个demo

## 安装 Appium
这里注意要使用 sudo，否则可能出现权限问题

        sudo npm i --location=global appium

安装完成后运行appium命令

        appium
        
控制台会出现已经安装成功的appium信息

        [Appium] Welcome to Appium v2.2.2


## 安装 UiAutomator2 Driver
安装前需要确保，这里不做介绍
* Android SDK platform tools、 Android platform 和 ANDROID_HOME 已经下载且环境变量已设置
* Java JDK已下载，且为 JDK 8 ，环境变量已设置（其他版本的未做验证）

执行以下命令，安装 uiautomator2

        appium driver install uiautomator2

安装成功后会显示类似如下

        Attempting to find and install driver 'uiautomator2'
        ✔ Installing 'uiautomator2' using NPM install spec 'appium-uiautomator2-driver'
        Driver uiautomator2@2.0.5 successfully installed
        - automationName: UiAutomator2
        - platformNames: ["Android"]


安装完成后再次运行 appium，能够看到如下

        [Appium] Available drivers:
        [Appium]   - uiautomator2@2.0.5 (automationName 'UiAutomator2')


## 安装 Appium client & 写一个demo

Appium Client 和 Seleium Client有严格的版本对应关系，如果版本指定错误或者使用默认的版本，则大概率会出现错误，需要浪费大量时间排查，所以这里 \ 
需要有2个注意的地方
* Appium Java Client  Selenium client 版本对应关系
* 添加依赖

### Appium Java Client  Selenium client 版本对应关系
| Appium Java Client  | Selenium client                                                    |
|---------------------|--------------------------------------------------------------------|
| 9.0.0               | 4.14.1                                                             |      
| N/A                 | 4.14.0                                                             |
| 8.5.0, 8.5.1, 8.6.0 | 4.9.1, 4.10.0, 4.11.0, 4.12.0, 4.12.1 (known issue: #2004), 4.13.0 |
| 8.4.0               | 4.8.2, 4.8.3, 4.9.0                                                |
| 8.3.0               | 4.7.0, 4.7.1, 4.7.2, 4.8.0, 4.8.1                                  |
| 8.2.1               | 4.5.0, 4.5.1, 4.5.2, 4.5.3, 4.6.0                                  |


### 添加依赖

通过以下方式添加依赖，例如 X.Y.Z： 8.2.1  A.B.C：4.5.0

Maven downloads dependency of the latest version matching the declared range by default, in other words whenever new \ 
versions of Selenium 4 libraries are published they are pulled transitively as Appium Java Client dependencies at the \ 
first project (re)build automatically.

In order to pin Selenium dependencies they should be declared in pom.xml in the following way:

```
<dependencies>
    <dependency>
        <groupId>io.appium</groupId>
        <artifactId>java-client</artifactId>
        <version>X.Y.Z</version>
        <exclusions>
            <exclusion>
                <groupId>org.seleniumhq.selenium</groupId>
                <artifactId>selenium-api</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.seleniumhq.selenium</groupId>
                <artifactId>selenium-remote-driver</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.seleniumhq.selenium</groupId>
                <artifactId>selenium-support</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-api</artifactId>
        <version>A.B.C</version>
    </dependency>
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-remote-driver</artifactId>
        <version>A.B.C</version>
    </dependency>
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-support</artifactId>
        <version>A.B.C</version>
    </dependency>
</dependencies>
```



### 编写demo

```
        UiAutomator2Options options = new UiAutomator2Options()
                .setApp("/Users/Desktop/app-debug.apk");
        AndroidDriver driver = new AndroidDriver(
                // The default URL in Appium 1 is http://127.0.0.1:4723/wd/hub
                new URL("http://127.0.0.1:4723"), options
        );
        try {
            WebElement el = driver.findElement(AppiumBy.xpath("//*[contains(@text, \"Hello\")]"));
            el.click();
            driver.getPageSource();
        } finally {
            driver.quit();
        }
```
