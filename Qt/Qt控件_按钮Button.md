## Qt按钮

Qt内置6种按钮，包括：

1.  QPushButton: 下压按钮，继承 QAbstractButton 类，被 QCommandLinkButton 继承。通常用于执行命令或触发事件。
2. QToolButton: 工具按钮，继承 QAbstractButton 类。是一种用于命令或者选项的可以快速访问的按钮，通常在 ToolBar 里面。工具按钮通常显示的是图标，而不是文本标签。 ToolButton 支持自动浮起。在自动浮起模式中，按钮只有在鼠标指向它的时候才绘制三维的框架。
3. QRadioButton: 选择按钮，继承 QAbstractButton 类。 Ra dioButton 单选按钮（单选框）通常成组出现，用于提供两个或多个互斥选项。
4. QCheckBox: 检查框， 继承 QAbstractButton 。复选按钮（复选框）与 RadioButton 的区别是选择模式，单选按钮提供多选一，复选按钮提供多选多。
5. QCommandLinkButton: 命令链接按钮，控件中文名是“命令链接按钮”。 QCommandLinkButton 继承QPushButton 。 Q CommandLinkButton 控件和 RadioButton 相似，都是用于在互斥选项中选择一项。表面上同平面按钮一样，但是 CommandLinkButton 除带有正常的按钮上的文字描述文本外，默认情况下，它也将携带一个箭头图标，表明按下按钮将打开另一个窗口或页面。
6. QDialogButtonBox: 对话框按钮，按钮盒子（按钮框），是由 QDialogButtonBox 类包装成的。QDialogButtonBox 继承 QWidget 。常用于对话框里自定义按钮，比如“确定”和 “取消 按钮。

### QPushButton

QPushButton: 下压按钮，继承 QAbstractButton 类，被 QCommandLinkButton 继承。通常用于执行命令或触发事件。



使用方法：

1. 包含头文件： `#include <QPushButton>` 
2. 声明需要的按钮： `QPushButton * button;`
3. 实例化按钮： `button = new QPushButton(this);` 需要传入参数，表明将该button放置于哪个控件下。
4. 配置按钮样式： 包括大小位置、按钮描述文字、文字和按钮样式等等。
5. 绑定信号和槽函数。



QPushButton 使用相对较为简单，无Demo。



### QToolButton

QToolButton: 工具按钮，继承 QAbstractButton 类。是一种用于命令或者选项的可以快速访问的按钮，通常在 ToolBar 里面。工具按钮通常显示的是图标，而不是文本标签。 ToolButton 支持自动浮起。在自动浮起模式中，按钮只有在鼠标指向它的时候才绘制三维的框架。



使用方法：

由于ToolButton防止在ToolBar里面，所以，在使用ToolButton时需要先创建一个ToolBar。

1. 包含头文件

    ```cpp
    #include <QToolBar>
    #include <QToolButton>
    ```

2. 声明ToolBar和ToolButton

    ```cpp
        QToolBar * mToolBar;
        QToolButton * mToolButton;
    ```

3. 实例化ToolBar和ToolButton

    ```cpp
        mToolBar = new QToolBar(this);
        mToolButton = new QToolButton(mToolBar);
    ```

    这里先实例化了一个ToolBar，放置于this也就是当前窗口中。然后创建一个ToolButton放置与ToolBar中。

4. 配置工具栏和按钮样式

    ```cpp
        mToolBar = new QToolBar(this);
        mToolBar->setGeometry(0, 0, 1280, 40);
    
        mToolButton = new QToolButton(mToolBar);
        mToolButton->setGeometry(0, 0, 60, 40);
        mToolButton->setText("Help");
    ```

5. 绑定信号和槽函数。

    为当前工具绑定特定的槽函数。



### QRadioButton

QRadioButton: 选择按钮，继承 QAbstractButton 类。 RadioButton 单选按钮（单选框）通常成组出现，用于提供两个或多个互斥选项。



示例：

头文件： `qbuttondemo.h`

```cpp
#ifndef QBUTTONDEMO_H
#define QBUTTONDEMO_H

#include <QMainWindow>
#include <QPushButton>
#include <QToolBar>
#include <QToolButton>
#include <QRadioButton>

class QButtonDemo : public QMainWindow
{
    Q_OBJECT

public:
    QButtonDemo(QWidget *parent = nullptr);
    ~QButtonDemo();

private:
    QPushButton * mPushButton;
    QToolBar * mToolBar;
    QToolButton * mToolButton;
    QRadioButton * mRadioButton_a;
    QRadioButton * mRadioButton_b;
};
#endif // QBUTTONDEMO_H

```





源文件： `qbuttondemo.cpp`

```cpp
#include "qbuttondemo.h"

QButtonDemo::QButtonDemo(QWidget *parent)
    : QMainWindow(parent)
{
    this->setGeometry(0, 0, 1280, 800);
    this->setWindowTitle("QButtonDemo");

    mPushButton = new QPushButton(this);
    mPushButton->setGeometry(580, 350, 120, 60);
    mPushButton->setText("Exit");

    mToolBar = new QToolBar(this);
    mToolBar->setGeometry(0, 0, 1280, 40);
    mToolButton = new QToolButton(mToolBar);
    mToolButton->setGeometry(0, 0, 60, 40);
    mToolButton->setText("Help");

    mRadioButton_a = new QRadioButton(this);
    mRadioButton_b = new QRadioButton(this);
    mRadioButton_a->setGeometry(0, 100, 120, 60);
    mRadioButton_b->setGeometry(120, 100, 120, 60);
    mRadioButton_a->setText("a");
    mRadioButton_b->setText("b");
}

QButtonDemo::~QButtonDemo() {}

```



主文件： `main.cpp`

```cpp
#include "qbuttondemo.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    QButtonDemo w;
    w.show();
    return a.exec();
}

```

执行应用程序。

![image-20240504154208402](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240504154208402.png)





### QCheckBox

QCheckBox: 检查框， 继承 QAbstractButton 。复选按钮（复选框）与 RadioButton 的区别是选择模式，单选按钮提供多选一，复选按钮提供多选多。



实例：

头文件： `qbuttondemo.h`

```cpp
#ifndef QBUTTONDEMO_H
#define QBUTTONDEMO_H

#include <QMainWindow>
#include <QPushButton>
#include <QToolBar>
#include <QToolButton>
#include <QRadioButton>
#include <QCheckBox>

class QButtonDemo : public QMainWindow
{
    Q_OBJECT

public:
    QButtonDemo(QWidget *parent = nullptr);
    ~QButtonDemo();

private:
    QPushButton * mPushButton;
    QToolBar * mToolBar;
    QToolButton * mToolButton;
    QRadioButton * mRadioButton_a;
    QRadioButton * mRadioButton_b;
    QCheckBox * mCheckBox_a;
    QCheckBox * mCheckBox_b;
    QCheckBox * mCheckBox_c;
    QCheckBox * mCheckBox_d;
};
#endif // QBUTTONDEMO_H

```



源文件： `qbuttondemo.cpp`

```cpp
#include "qbuttondemo.h"

QButtonDemo::QButtonDemo(QWidget *parent)
    : QMainWindow(parent)
{
    this->setGeometry(0, 0, 1280, 800);
    this->setWindowTitle("QButtonDemo");

    mPushButton = new QPushButton(this);
    mPushButton->setGeometry(580, 350, 120, 60);
    mPushButton->setText("Exit");

    mToolBar = new QToolBar(this);
    mToolBar->setGeometry(0, 0, 1280, 40);
    mToolButton = new QToolButton(mToolBar);
    mToolButton->setGeometry(0, 0, 60, 40);
    mToolButton->setText("Help");

    mRadioButton_a = new QRadioButton(this);
    mRadioButton_b = new QRadioButton(this);
    mRadioButton_a->setGeometry(0, 100, 60, 60);
    mRadioButton_b->setGeometry(60, 100, 60, 60);
    mRadioButton_a->setText("a");
    mRadioButton_b->setText("b");

    mCheckBox_a = new QCheckBox(this);
    mCheckBox_a->setGeometry(180, 100, 60, 60);
    mCheckBox_a->setText("a");
    mCheckBox_b = new QCheckBox(this);
    mCheckBox_b->setGeometry(240, 100, 60, 60);
    mCheckBox_b->setText("b");
    mCheckBox_c = new QCheckBox(this);
    mCheckBox_c->setGeometry(300, 100, 60, 60);
    mCheckBox_c->setText("c");
    mCheckBox_d = new QCheckBox(this);
    mCheckBox_d->setGeometry(360, 100, 60, 60);
    mCheckBox_d->setText("d");
}

QButtonDemo::~QButtonDemo() {}

```



主文件： `main.cpp`

```cpp
#include "qbuttondemo.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    QButtonDemo w;
    w.show();
    return a.exec();
}

```



运行效果：

![image-20240504154745754](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240504154745754.png)





### QCommandLinkButton

QCommandLinkButton 控件中文名是“命令链接按钮”。 QCommandLinkButton 继承QPushButton 。 CommandLinkButton 控件和 RadioButton 相似，都是用于在互斥选项中选择一项。表面上同平面按钮一样，但是 CommandLinkButton 除带有正常的按钮上的文字描述文本外，默认情况下，它也将携带一个箭头图标 ，表明按下按钮将打开另一个窗口或页面。



```cpp
#include <QApplication>
#include <QCommandLinkButton>

class QButtonDemo : public QMainWindow
{
    Q_OBJECT

public:
    QButtonDemo(QWidget *parent = nullptr);
    ~QButtonDemo();

private:
    QCommandLinkButton * mCommandLinkButton_a;
    QCommandLinkButton * mCommandLinkButton_b;
};

QButtonDemo::QButtonDemo(QWidget *parent)
    : QMainWindow(parent)
{
    this->setGeometry(0, 0, 1280, 800);
    this->setWindowTitle("QButtonDemo");
        
    mCommandLinkButton_a = new QCommandLinkButton(this);
    mCommandLinkButton_a->setGeometry(480, 100, 60, 60);
    mCommandLinkButton_a->setText("a");
    mCommandLinkButton_b = new QCommandLinkButton(this);
    mCommandLinkButton_b->setGeometry(540, 100, 60, 60);
    mCommandLinkButton_b->setText("b");
}

QButtonDemo::~QButtonDemo() {}

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    QButtonDemo w;
    w.show();
    return a.exec();
}

```





运行结果：

![image-20240504155754980](https://klelee-images.oss-cn-qingdao.aliyuncs.com/typora/image-20240504155754980.png)





### QDialogButtonBox

QDialogButtonBox: 对话框按钮，按钮盒子（按钮框），是由 QDialogButtonBox 类包装成的。QDialogButtonBox 继承 QWidget 。常用于对话框里自定义按钮，比如“确定”和 “取消 按钮。



还没理解！！！！

