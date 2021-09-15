---
title: Angular依赖注入学习笔记
tags: [Angular]
categories: learning
toc: true
mathjax: true
---
 Angular DI 框架学习笔记
 <!-- more -->

#### 最简单的依赖注入
``` bash
class Person {
  constructor(address, id) {
    this.address = address;
    this.id = id;
  }
}
```
* 构造函数中传入的adress,id即为注入的依赖


#### angular DI框架
* 在 Angular 中，依赖注入包括以下三个部分：
  * Injector（注入者）：使用 Injector 提供的 API 创建依赖的实例
  * Provider（提供者）：Provider 告诉 Injector 怎样 创建实例（比如我们上面提到的是通过某个构造函数还是工厂类创建等等）。Provider 接受一个令牌，然后把令牌映射到一个用于构建目标对象的工厂函数。
  * Dependency（依赖）：依赖是一种 类型 ，这个类型就是我们要创建的对象的类型。

* Angular 中依赖对象的创建方式分为以下四种：
  * useClass
  * useValue
  * useExisting(指定别名时，要确保只生成一个实例，就要用 useExisting 来指定别名)
  * useFactory

* 相应地，在 Angular 中 Provider 主要分为：
  * ClassProvider
  * ValueProvider
  * ExistingProvider
  * FactoryProvider

* 使用 @Injectable() 的 providedIn 属性优于 @NgModule() 的 providers 数组，因为使用 @Injectable() 的 providedIn 时，优化工具可以进行摇树优化，从而删除你的应用程序中未使用的服务，以减小捆绑包尺寸。

* 通过依赖注入可以实现Angular的状态管理。

#### 示例对比-使用DI框架

* hero.component.ts
``` bash
import { Component } from '@angular/core';

@Component({
  selector: 'app-heroes',
  template: `
    <h2>Heroes</h2>
    <app-hero-list></app-hero-list>
  `
})
export class HeroesComponent { }

```

* hero.ts
``` bash
export interface Hero {
  id: number;
  name: string;
  isSecret: boolean;
}
```

* mock-heroes.ts
``` bash
import { Hero } from './hero';

export const HEROES: Hero[] = [
  { id: 11, isSecret: false, name: 'Dr Nice' },
  { id: 12, isSecret: false, name: 'Narco' },
  { id: 13, isSecret: false, name: 'Bombasto' },
  { id: 14, isSecret: false, name: 'Celeritas' },
  { id: 15, isSecret: false, name: 'Magneta' },
  { id: 16, isSecret: false, name: 'RubberMan' },
  { id: 17, isSecret: false, name: 'Dynama' },
  { id: 18, isSecret: true,  name: 'Dr IQ' },
  { id: 19, isSecret: true,  name: 'Magma' },
  { id: 20, isSecret: true,  name: 'Tornado' }
];

```

* hero-list.component.ts 
``` bash
import { Component }   from '@angular/core';
import { HEROES }      from './mock-heroes';

@Component({
  selector: 'app-hero-list',
  template: `
    <div *ngFor="let hero of heroes">
      {{hero.id}} - {{hero.name}}
    </div>
  `
})
export class HeroListComponent {
  heroes = HEROES;
}
```


#### 示例对比-使用DI框架
* mock-heroes.ts
``` bash
import { Hero } from './hero';

export const HEROES: Hero[] = [
  { id: 11, isSecret: false, name: 'Dr Nice' },
  { id: 12, isSecret: false, name: 'Narco' },
  { id: 13, isSecret: false, name: 'Bombasto' },
  { id: 14, isSecret: false, name: 'Celeritas' },
  { id: 15, isSecret: false, name: 'Magneta' },
  { id: 16, isSecret: false, name: 'RubberMan' },
  { id: 17, isSecret: false, name: 'Dynama' },
  { id: 18, isSecret: true,  name: 'Dr IQ' },
  { id: 19, isSecret: true,  name: 'Magma' },
  { id: 20, isSecret: true,  name: 'Tornado' }
];

```

* hero.service.ts
``` bash
import { Injectable } from '@angular/core';
import { HEROES } from './mock-heroes';

@Injectable({
  // we declare that this service should be created
  // by the root application injector.
  providedIn: 'root',
})
export class HeroService {
  getHeroes() { return HEROES; }
}
```
* hero-list.component.ts
``` bash
import { Component }   from '@angular/core';
import { Hero }        from './hero';
import { HeroService } from './hero.service';

@Component({
  selector: 'app-hero-list',
  template: `
    <div *ngFor="let hero of heroes">
      {{hero.id}} - {{hero.name}}
    </div>
  `
})
export class HeroListComponent {
  heroes: Hero[];

  constructor(heroService: HeroService) {
    this.heroes = heroService.getHeroes();
  }
}
```



#### 更多相关知识可参考[angular官方文档](https://angular.io/guide/dependency-injection)

