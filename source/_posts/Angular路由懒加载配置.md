---
title: Angular路由懒加载配置
date: 2021-02-01 19:37:00
categories:
- 前端
tags:
- Angular
---

# @NgModule的作用
- NgModule 最根本的意义是帮助开发者组织业务代码，开发者可以利用 NgModule 把关系比较紧密的组件组织到一起，这是首要的。
- NgModule 用来控制组件、指令、管道等是否可以使用，处于同一个 NgModule 里面的组件默认互相可见，而对于外部的组件来说，只能看到 NgModule 导出（ exports ）的内容，也就是说，如果你定义的 NgModule 不 exports 任何内容，那么外部使用者即使 import 了你这个模块，也没法使用里面定义的任何内容。
- NgModule 是打包时候用到的最小单位，打包的时候会检查所有 @NgModule 和路由配置，Angular底层是使用webpack打包。因为Angular已经帮我们配置好了webpack，所以开发者轻松很多，否则就需要自己配置环境。
- NgModule 是 Router 进行异步加载的最小单位，Router 能加载的最小单位是模块，而不是组件。当然，模块里面只放一个组件是允许的，很多组件库都是这样做的。

# @NgModule结构说明
``` javascript
    @NgModule({ 　　
        declarations: [], //属于当前模块的组件、指令及管道　　
        imports: [], //当前模板所依赖的项，即外部模块（包括httpModule、路由等）　　
        export:[],//声明出应用给其他的module使用　　
        providers: [], //注入服务到当前模块　　
        bootstrap: []//默认启动哪个组件(只有根模块才能设置bootstrap属性)
    })
```

# 懒加载说明
（1）RouterModule对象提供了两个静态的方法:forRoot()和forChild()来配置路由信息。
- forRoot()//在主模块中定义主要的路由信息
- forChild()//应用在特性模块（子模块）中

（2）懒加载：loadChildren
此处并没有将对应的模块加入到AppModule中，而是通过loadChildren属性，告诉Angular路由依据loadChildren属性配置的路径去加载对应的模块。这就是模块懒加载功能的具体应用，当用户访问 /xxx/** 路径的时候，才会加载对应的模块，这减少了应用启动时加载资源的大小。 loadChildren的属性值由三部分组成：
- 需要导入Module的相对路径
- #分隔符
- 导出模块类的名称

（3）预加载
在使用懒加载的情况下，路由第一次加载某个模块时，有时反应有延迟。这时就可以用预加载策略来解决这个问题。
Angular提供了两种加载策略，
- PreloadAllModules-预加载
- NoPreloading-没有预加载（默认）。

RouterModule.forRoo()的第二个参数可以添加配置选项，配置选项中就有一个是preloadingStrategy配置，这个配置是一个预加载策略配置。

``` javascript
    //使用默认预加载-预加载全部模块
    import { NgModule } from '@angular/core'; 
    import { AppComponent } from './app.component'; 
    import { routes } from './main.routing'; 
    import { RouterModule } from '@angular/router'; 
    import { PreloadAllModules } from '@angular/router'; 
    @NgModule({ 　　
        declarations: [AppComponent], 　　
        imports: [ BrowserModule, RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules }) ], 　　
        providers: [], 　　
        bootstrap: [AppComponent] 
    }) 
    export class AppModule { }
```

# 自定义预加载策略
- 自定义5秒后加载所有模块
在app组件的同级新建一个custom-preloading-strategy.ts文件
``` javascript
    import { Route } from '@angular/router';
    import { PreloadingStrategy } from '@angular/router';
    import { Observable } from 'rxjs/Rx';

    export class CustomPreloadingStrategy implements PreloadingStrategy {
        preload(route: Route, fn: () => Observable<boolean>): Observable<boolean> {
            return Observable.of(true).delay(5000).flatMap((_: boolean) => fn());
        }
    }
```
在app.modules.ts的providers中注入
``` javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { routes } from './main.routing';
    import { RouterModule } from '@angular/router';
    import { CustomPreloadingStrategy } from './preload';

    @NgModule({
        declarations: [
            AppComponent,
            HomeComponent
        ],
        imports: [
            BrowserModule,
            RouterModule.forRoot(routes, { preloadingStrategy:  CustomPreloadingStrategy })
        ],
        providers: [CustomPreloadingStrategy ],
        bootstrap: [AppComponent]
    })
    export class AppModule { }
```

- 自定义-指定模块预加载
在app组件的同级新建一个selective-preloading-strategy.ts文件
（需要在app-routing.module.ts中的providers注入，然后在路由中定义的data通过附加参数来设置是否预加载）
``` javascript
    import { Injectable } from '@angular/core';
    import { PreloadingStrategy, Route } from '@angular/router';
    import { Observable} from 'rxjs/Observable';
    import 'rxjs/add/observable/of';
    @Injectable()
    export class SelectivePreloadingStrategy implements PreloadingStrategy {
        preloadedModules: string[] = [];

        preload(route: Route, load: () => Observable<any>): Observable<any> {
            if (route.data && route.data['preload']) {
            return load();
            } else {
            return Observable.of(null);
            }
        }
    }
```
app-routing.module.ts（此文件懒加载与预加载相结合）
``` javascript
    import { NgModule } from '@angular/core';
    import { RouterModule, Routes } from '@angular/router';
    import { CanDeactivateGuard } from './guard/can-deactivate-guard.service';
    import { SelectivePreloadingStrategy } from './selective-preloading-strategy'; // 预加载
    import { PageNotFoundComponent } from './not-found.component';
    const appRoutes: Routes = [
        { path: '', redirectTo: 'home', pathMatch: 'full'},
        { path: 'mian', loadChildren: './main/mian.module#MainModule' }, // 懒加载(在这个层级的router配置文件及module文件都不需要引入该组建)
        { path: 'home', loadChildren: './home/home.module#HomeModule', data: { preload: true } }, // 懒加载 + 预加载
        { path: '**', component: PageNotFoundComponent } // 注意要放到最后
    ];
    @NgModule({
    　　imports: [
    　　　　RouterModule.forRoot(appRoutes,{
    　　　　　　enableTracing: true, // <-- debugging purposes only
    　　　　　　preloadingStrategy: SelectivePreloadingStrategy // 预加载
    　　　　})
    　　],
    　　exports: [
    　　　　RouterModule
    　　],
    　　providers: [
    　　　　CanDeactivateGuard,
    　　　　SelectivePreloadingStrategy
    　　]
    })
    export class AppRoutingModule { }
```
