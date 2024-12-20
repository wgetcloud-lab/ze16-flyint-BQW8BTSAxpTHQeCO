
# 华为鸿蒙开发：自定义组件的创建与使用


![](https://i-blog.csdnimg.cn/direct/4911dd49c3b042ff8c304707a5d6deb8.png#pic_center)


## 引言


在华为鸿蒙应用的开发中，自定义组件是构建复杂界面和重用代码的重要手段。通过自定义组件，开发者可以将复杂的UI逻辑封装起来，提高代码的可维护性和可读性。本文将通过 DevEco Studio 详细介绍如何在鸿蒙应用中创建和使用自定义组件，包括组件的参数传递、状态管理、事件处理以及如何通过`@Builder`和`@BuilderParam`装饰器自定义组件内容。


## 自定义组件基础


自定义组件允许我们将UI逻辑封装在一个可重用的单元中。


### 示例1：简单的自定义组件



```
@Component
struct MyButton {
  @State count: number = 1
  build() {
    Row() {
      Text('点击次数：' + this.count.toString())
      Button('点击我').onClick(() => {
        this.count++
      })
    }
    .justifyContent(FlexAlign.SpaceBetween)
    .padding(10)
    .border({ width: 1 })
    .width('100%')
  }
}

@Entry
@Component
struct Index {
  build() {
    Column() {
      MyButton()
      MyButton({ count: 10 })
      MyButton()
    }
    .padding(20)
  }
}

```

在这个示例中，我们创建了一个名为`MyButton`的自定义组件，它显示了按钮的点击次数，并在每次点击时增加计数。


## 参数传递和状态管理


自定义组件可以接受参数，这些参数可以用于设置组件的初始状态。


### 示例2：传递参数的自定义组件



```
@Component
struct MyFoodComponent {
  @State food: string = '番茄炒蛋'
  dog: string = '泰迪' // 普通变量也是响应式的

  sayHello = () => {
    console.log('默认的打招呼')
  }

  build() {
    Row() {
      Text(`自定义组件 ${this.dog} 喜欢吃 ${this.food}`)
      Button('点击按钮')
        .onClick(() => {
          this.food += '!'
          this.dog += '.'
          this.sayHello()
        })
    }
    .border({ width: 1 })
    .padding(20)
  }
}

@Entry
@Component
struct Index {
  build() {
    Column() {
      MyFoodComponent()

      MyFoodComponent({
        dog: '金毛',
        food: '牛排',
        sayHello() {
          console.log('外部传入的打招呼的逻辑')
        }
      })
      .border({ width: 1 })
      .padding(20)

      MyFoodComponent()
    }
    .padding(20)
  }
}

```

在这个示例中，`MyFoodComponent`组件接受`dog`和`food`作为参数，并允许外部传入`sayHello`函数来覆盖默认的打招呼逻辑。


## 事件处理和回调


自定义组件可以定义事件处理函数，这些函数可以在组件内部或外部被调用。


### 示例3：事件处理的自定义组件



```
@Component
struct CardComponent {
  title: string = ''
  subTitle: string = ''

  clickHandler = () => {
    AlertDialog.show({
      message: '默认的功能'
    })
  }

  build() {
    Column() {
      Row() {
        Text(this.title).layoutWeight(1)
        Row() {
          Text(this.subTitle)
          Image($r('app.media.ic_public_arrow_right'))
            .width(20)
        }
        .onClick(() => {
          this.clickHandler()
        })
      }
      Row() {
        Text('默认的内容')
      }
      .height(100)
    }
    .backgroundColor(Color.Pink)
    .borderRadius(10)
    .padding(10)
    .margin({ bottom: 20 })
  }
}

@Entry
@Component
struct Index {
  build() {
    Column() {
      CardComponent({
        title: '所有评论',
        subTitle: '查看更多',
        clickHandler() {
          AlertDialog.show({
            message: '查看更多评论'
          })
        }
      })

      CardComponent({
        title: '所有商品(100+)',
        subTitle: '查看更多',
        clickHandler() {
          AlertDialog.show({
            message: '查看更多商品'
          })
        }
      })
    }
    .padding(20)
  }
}

```

在这个示例中，`CardComponent`组件接受`title`、`subTitle`和`clickHandler`作为参数，允许外部传入点击事件的处理逻辑。


## 使用`@Builder`和`@BuilderParam`自定义组件内容


`@Builder`和`@BuilderParam`装饰器允许我们自定义组件的内容。


### 示例4：使用`@Builder`和`@BuilderParam`的自定义组件



```
@Component
struct CardComponent {
  title: string = ''
  subTitle: string = ''

  clickHandler = () => {
    AlertDialog.show({
      message: '默认的功能'
    })
  }

  @Builder
  defaultBuilder() {
    Text('默认内容')
  }

  @BuilderParam
  contentBuilder: () => void = this.defaultBuilder

  build() {
    Column() {
      Row() {
        Text(this.title).layoutWeight(1)
        Row() {
          Text(this.subTitle)
          Image($r('app.media.ic_public_arrow_right'))
            .width(20)
        }
        .onClick(() => {
          this.clickHandler()
        })
      }
      Row() {
        this.contentBuilder()
      }
      .height(100)
    }
    .backgroundColor(Color.Pink)
    .borderRadius(10)
    .padding(10)
    .margin({ bottom: 20 })
  }
}

@Entry
@Component
struct Index {
  build() {
    Column() {
      CardComponent({
        title: '所有评论',
        subTitle: '查看更多',
        clickHandler() {
          AlertDialog.show({
            message: '查看更多评论'
          })
        }
      }) {
        Text('这里是自定义内容')
      }

      CardComponent({
        title: '所有商品(100+)',
        subTitle: '查看更多',
        clickHandler() {
          AlertDialog.show({
            message: '查看更多商品'
          })
        }
      })
    }
    .padding(20)
  }
}

```

在这个示例中，`CardComponent`组件使用`@Builder`和`@BuilderParam`装饰器来自定义组件的内容。


## 结语


通过本文的详细介绍和示例，你应该能够掌握在华为鸿蒙开发中创建和使用自定义组件的方法。这些技能对于开发具有复杂界面和逻辑的鸿蒙应用至关重要。如果你有任何问题或想要进一步讨论，欢迎在评论区留下你的想法。




---


以上就是一篇关于华为鸿蒙开发中自定义组件的详细教程。希望这篇文章能帮助你更好地理解和使用华为鸿蒙开发中的自定义组件。如果你在使用 DevEco Studio 进行开发时遇到任何问题，欢迎交流讨论。


 本博客参考[蓝猫机场](https://fenfang.org)。转载请注明出处！
