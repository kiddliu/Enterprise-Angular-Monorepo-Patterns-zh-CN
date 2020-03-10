# 第二部分 用库组织代码

## 简介

&emsp;&emsp;随着时间发展，应用无论在大小还是复杂度上都会增加，尤其是当多个应用需要共享代码的时候更加严重。于是，大型组织与业务需要考虑在大小以及复杂度上拆分应用、组件复用，从而在如何实现的角度上保持一致。

&emsp;&emsp;库，作为执行特定任务的一组相关文件，以模块化的方式对开发有帮助。在包的`index.ts`文件中包含了一组定义良好的公共API，指明如何使用这个包。开发者也应该包含一个README文件，用来记录库的行为以及指定的代码所有者（更多信息请查看第五部分的“代码所有权”一节）。

&emsp;&emsp;在Nx工作区里，库需要修饰符来描述它的内容与预期目的。这些修饰符帮助我们组织库，并提供一种可视化的区分它们的方式。这里有两种基本的修饰符：“范围”与“type”。也可以加入额外的修饰符描述特定组织内的不同场景，比如“platform”。

#### 范围

&emsp;&emsp;范围与逻辑分组、业务用例或是领域相关。我们的实例应用中的范围示例有`seatmap`、`booking`、`shared`以及`check-in`。它们各自包含了一组用来管理应用逻辑子领域（sub-domain）的包。

<hr>

&emsp;&emsp;我们推荐使用**文件夹结构来标记范围**

&emsp;&emsp;下边的文件夹结构是一个示例范围结构，用来描述座位图功能：

    shared/
        seatmap/
            feature/

&emsp;&emsp;这里，“shared”与“seatmap”都是用来分组的文件夹，而`feature`是一个嵌套两层深的库。这样做就很清晰了：这个功能是属于`shared`的子领域`seatmap`的。

&emsp;&emsp;这个库使用的标签就是`scope:shared`，这是一个顶层范围。

<hr>

#### 类型

&emsp;&emsp;类型与库的内容有关，并指明它的目的与用途。类型的示例有`ui`、`data-access`以及`feature`。具体请看下边的讨论。

<hr>

&emsp;&emsp;我们推荐使用**前缀与标记来表示类型**。我们还推荐限制类型的数量为这一节描述的那4个。

&emsp;&emsp;这个功能的文件夹名于是就是`feature-shell`，它使用了库类型作为前缀。

&emsp;&emsp;那么上一个例子中座位图功能库的标签现在就变成了`scope:shared,type:feature`。

<hr>

#### 平台

&emsp;&emsp;我们可以用其他标识符来区分类似的库（比如为了区分`server`、`mobile`以及`desktop`）。**频台**就是一种这样的标识符。

<hr>

&emsp;&emsp;我们推荐使用**标签来标记平台**。

&emsp;&emsp;座位图功能最终的标签于是变成了`scope:shared,type:feature,platform:desktop`。

<hr>

&emsp;&emsp;每一个库都应该存放在其范围定义的文件夹树中，有着类似上边的`scope:SCOPE,type:TYPE,platform:PLATFORM`格式的标签，并以它的类型为名称前缀。对于标签是如何使用的，请查看第三部分的“对库的依赖项进行强制限制”一节。

<hr>

&emsp;&emsp;*不要按文件类型分文件夹*
    
&emsp;&emsp;我们强烈不鼓励文件按类似`directives/`、`services/`这样的方式组织。原因是当我们尝试做修改的时候，我们常常需要同一时刻修改一系列相关的文件。如果按照文件类型组织，我们就会因为需要找到需要的文件而频繁遍历文件夹树。
    
&emsp;&emsp;我们更建议代码按照领域组织，把所有相关的文件，比如`airline`包含的状态、ui组件等等都放在一个文件夹内。这使得开发者可以直接工作在相关的文件上而无需频繁地遍历文件夹树。

<hr>

## 库的类型

&emsp;&emsp;可以在工作区中使用的库有很多种。为了维持一定意义上的秩序，我们推荐只使用4类库：

* 功能库：开发者把程序库看作是应用程序中为特定业务场景（用注入服务）实现的智能UI或是页面。
* UI库：UI库只包含`representational`组件。
* 数据访问库：数据访问库包含了与后端系统交互的服务与工具。也包括所有与状态管理相关的代码。
* 工具库：工具库包含了为许多库与应用使用的通用工具与服务。

&emsp;&emsp;在了解所有的库类型之前，让我们迅速的介绍一些受Flux影响的组件类型基础知识，从而方便理解智能型与展示型组件。

### 旁白：智能型与展示型组件

&emsp;&emsp;通过单项数据流构建应用的时候，有两类组件：一种是通过接收发送数据与应用的其他部分通信的组件（称为“智能型”组件），另一种只接收数据（成为“展示型”或是“惰性”组件）。

#### 智能型组件

&emsp;&emsp;这类组件管理或委托业务逻辑，且使用依赖注入系统注入服务。它们可以通过分发各种动作把更新发送到应用的其他部分。它们把展示型组件作为子实例：智能型组件通过管道把数据发送到子展示型组件，而后应用程序响应子组件发出的必要的事件。

#### 展示型组件

&emsp;&emsp;这类组件几乎没有任何业务逻辑。它们**只**通过*输入*与*输出*与外界通信。它们唯一的目的是渲染数据并接收用户输入——但是并不处理输入。它们会通过*输出*发送事件到知道如何处理它们的父组件。

&emsp;&emsp;它们的复用度高，易于测试，可以做到完全通用/领域无关（好比数据—表）或是有一个领域上下文（比如记录-预定-表）。

&emsp;&emsp;至此我们了解了关于智能型与展示型组件的术语，接下来让我们了解以下包含它们的库的类型。功能库包含智能型组件，而UI库包含展示型组件。

### 功能库

#### 定义

&emsp;&emsp;功能库包含了一组配置应用程序业务场景或是页面的文件。这类库都包含了一个描述部分应用程序行为的ngModule（也可能包含了一部分NgRx状态，处理了应用特定部分内部的路由，并且可以被应用懒加载）。

&emsp;&emsp;库的绝大部分组件都是可以与NgRx状态交互的智能型组件。这一类型的库也可以包含绝大部分的UI逻辑、表单验证代码等等。功能库几乎都是针对特定应用的，常常是懒加载的。

#### 命名规则

&emsp;&emsp;`feature`（如果嵌套的话）或者是`feature-*`（比如`feature-shell`）。

    libs/
        booking/
            feature-shell/ ①
                src/
                    index.ts
                    lib/
                        booking-feature-shell.module.ts

① `feature-shell`是针对特定应用的功能库（在这个例子中，就是"booking"应用）。

> 在我们的示例应用中, nrwl航空想要开始开发订票应用的UI。我们打算为用户提供三个不同界面：搜索航班，乘客信息，以及座位图。

&emsp;&emsp;让我们首先看看，把这些界面的路由直接放在`booking/desktop`应用中。

```typescript
export routes = [
  {
  path: '',
  pathMatch: 'full',
  component: 'FlightSearchComponent'
  },
  {
  path: '/passenger',
  pathMatch: 'full',
  loadChildren: '@nrwl-airlines/booking/feature-passengerinfo#BookingFeaturePassengerInfoModule'
  },
  {
  path: '/seatmap',
  pathMatch: 'full',
  loadChildren: '@nrwl-airlines/shared/seatmap/feature-seatlisting#SharedSeatmapFeatureSeatListingModule'
  }
]
```

&emsp;&emsp;假如这个路由是放在`apps/booking/desktop`里边，我们就需要在`apps/booking/mobile`里边复制一份，之后保证二者之间一直同步。鉴于不做重复性工作，我们应该想要把这段路由提取到一个公共文件中去。

&emsp;&emsp;我们可以把这段路由挪到一个库去。这个库会包含`booking`的路由结构并执行任何初始化代码。这个库的模块（module）也可以被懒加载到父级应用`booking/desktop`以及`booking/mobile`。

&emsp;&emsp;合适的命名规则是`BookingFeatureShellModule`，存放在如上所示的`libs/booking/feature-shell`。现在我们有了4个功能库：`booking/feature-shell`、`booking/feature-flight-search`、`booking/feature-passenger-info`以及`shared/seatmap/feature-seat-listing`。他们每一个都对应应用程序中的一个界面。

#### 功能库模块范例

```typescript
import { NgModule } from '@angular/core';
import { RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';
import { FlightSearchComponent } from './components/flight-search/flightsearch.component';

@NgModule({
  imports: [
    CommonModule,
    RouterModule.forChild([ ①
      {
        path: '',
        component: FlightSearchComponent,
        pathMatch: 'full'
      },
      {
        path: '/passenger',
        pathMatch: 'full',
        loadChildren: '@nrwl-airlines/booking/feature-passengerinfo#BookingFeaturePassengerInfoModule'
      },
      {
        path: '/seatmap',
        pathMatch: 'full',
        loadChildren: '@nrwl-airlines/shared/seatmap/feature-seatlisting#SharedSeatmapFeatureSeatListingModule'
      }
    ])
  ],
  declarations: [FlightSearchComponent]
})
export class BookingFeatureShellModule {} ②
```

① 请注意`.forChild`的使用。在功能库中我们从来不会使用`.forRoot`——它只可以在应用中使用。

② 这个模块文件的路径应该是`libs/booking/feature-shell/src/booking-featureshell.module.ts`

&emsp;&emsp;关于在生成功能库懒加载样板文件的时候可用的命令行选项，请查看之后的“命令行选项”一节。

&emsp;&emsp;现在我们知道了在哪里放置智能型组件，接下来我们看看在哪里放置展示型组件。

### UI库

#### 定义

&emsp;&emsp;UI库是一组相互关联的展示型组件。通常是不会把服务注入到这类组件中去的（所有需要的数据都来自于*输入*）。

&emsp;&emsp;至于UI库是否应该针对特定应用、是否可以共享，请查阅附录丙（“我应该在什么地方创建新的库？”一节）。

#### 命名规则

&emsp;&emsp;`ui`（如果嵌套的话）或者`ui-*`（比如`ui-buttons`）

#### UI库模块范例

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ConfirmButtonComponent } from './confirm-button/confirmbutton.component';

@NgModule({
  imports: [CommonModule],
  declarations: [ConfirmButtonComponent],
  exports: [ConfirmButtonComponent]
  })
export class CommonUiButtonsModule {} ①
```
① 这个模块文件的路径应该是`libs/common/ui-buttons/src/common-ui-buttons.module.ts`。

&emsp;&emsp;除了智能型与惰性组件，还有数据访问库与工具库。接下来我们来了解它们都包含些什么。

### 数据访问库

#### 定义

&emsp;&emsp;数据访问库包含REST或是webSocket服务，作为客户端内的代理，提供服务层API。

&emsp;&emsp;所有与状态管理相关的文件也都放在数据访问文件夹内（按照惯例，它们被分组放在`src/lib`中的`+state`文件夹内）。

#### 命名规则

&emsp;&emsp;`data-access`（如果嵌套的话）或者`data-access-*`（比如`data-access-seatmap`）

#### 数据访问库模块范例

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { StoreModule } from '@ngrx/store';
import { EffectsModule } from '@ngrx/effects';
import { customersReducer } from './+state/state.reducer';
import { customersInitialState } from './+state/state.init';
import { CustomersEffects } from './+state/state.effects';
@NgModule({
  imports: [
    CommonModule,
    StoreModule.forFeature('customer', customersReducer, { ①
      initialState: customersInitialState
    }),
    EffectsModule.forFeature([CustomersEffects]) ①
  ],
  providers: [CustomersEffects]
})
export class CustomersDataAccessModule {}
```

① 再次注意，在库里边我们指挥用到`.forFeature`。任何`.forRoot`调用都应该出现在应用中。

&emsp;&emsp;复用数据访问库是很容易的，所以专注精神，创建更多的共享数据访问库吧。

![信息符号](info.png "信息符号")&emsp;&emsp;如果库是共享的话，请为它编写文档！为了解更多，请查看“为库编写文档” 一节。

### 工具库

#### 定义

&emsp;&emsp;工具库包含许多其他库使用的常见工具/服务模块。通常没有ngModule定义，整个库只是一组工具或是纯函数的集合。

#### 命名规则

&emsp;&emsp;`util`（如果嵌套的话）或者`util-*`（比如`util-testing`）

#### 工具模块范例

*libs/shared/util-formatting*

```typescript
export { formatDate, formatTime } from './src/format-date-fns';
export { formatCurrency } from './src/format-currency';
```

至此我们讲完了各种类型的库，于是我们可以介绍两个重要的概念：使用嵌套目录结构把库分组，鼓励使用共享库进行代码复用。

## 用来分组的文件夹

#### 定义

在我们用来参考的文件夹结构中，文件夹`libs/booking`、`libs/check-in`、`libs/shared`和`libs/shared/seatmap`是用来分组的。其中只包含库或是其他用来分组的文件夹。

使用这些文件夹的意义在于帮助我们按范围管理包。我们估计把（通常）一起更新的包分在一组里。这样可以帮助减少开发者在海量文件夹内寻找正确文件的时间。

    apps/
        booking/
        check-in/
    libs/
        booking/                <---- 用来分组的文件夹
            feature-shell/      <---- 库
            
    check-in/
        feature-shell/
        
    shared/                     <---- 用来分组的文件夹
        data-access/            <---- 库
        
    seatmap/                    <---- 用来分组的文件夹
        data-access/            <---- 库
        feature-seatmap/        <---- 库
