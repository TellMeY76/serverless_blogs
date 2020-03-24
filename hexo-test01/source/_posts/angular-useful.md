---
title: 10 Useful Angular Features You’ve Probably Never Used
tags: [Angular]
categories: learning
toc: true
mathjax: true
---
Having spent so much time writing Angular apps, you’re probably convinced you’ve seen it all. To be 100% sure — read on 😃
<!-- more -->

## Title

``` bash
import { Title } from "@angular/platform-browser"
@Component({
    ...
})
export class LoginComponent implements OnInit {
    constructor(private title: Title) {}
    ngOnInit() {
        title.setTitle("Login")
    }
}
```

When we navigate to the LoginComponent the title of the browser will be set to “Login”

## Meta

``` bash
import { Meta } from "@angular/platform-browser"
@Component({
    ...
})
export class BlogComponent implements OnInit {
    constructor(private meta: Meta) {}
    ngOnInit() {
        meta.updateTag({name: "title", content: ""})
        meta.updateTag({name: "description", content: "Lorem ipsum dolor"})
        meta.updateTag({name: "image", content: "./assets/blog-image.jpg"})
        meta.updateTag({name: "site", content: "My Site"})
    }
}
```

With this our BlogComponent can be rendered on Facebook, Twitter, etc describing our component, providing titles, images, and descriptions.

## Override Template interpolation

``` bash
@Component({
    interpolation: ["((","))"]
})
export class AppComponent {}
```

``` bash
@Component({
    template: `
        <div>
            ((data))
        </div>
    `,
    interpolation: ["((","))"]
})
export class AppComponent {
    data: any = "dataVar"
}
```

## Location

``` bash
import { Location } from "@angular/common"
@Component({

    ...

})
export class AppComponent {

    constructor(private location: Location) {}
    navigateTo(url) {
        this.location.go(url)
    }
    goBack() {
        location.back()
    }
    goForward() {
        location.forward()
    }

}
```

We can get the URL of the current browser window using Location service. Depending on which LocationStrategy is used, Location will either persist to the URL's path or the URL's hash segment

## @Attribute decorator

The values of Attribute decorator are checked once and never checked again. They are used similarly to @Input decorator

``` bash
@Component({
    ...
})
export class BlogComponent {
    constructor(@Attribute("type") private type: string ) {}
}
```

## HttpInterceptor

``` bash
@Injectable()
export class MockBackendInterceptor implements HttpInterceptor {
    constructor() {}
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        ...
    }
}
```

Then, insert it in your main module:

``` bash
@NgModule({
    ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MockBackendInterceptor,
            multi: true
        }
    ]
    ...
})
export class AppModule {}
```

## AppInitializer

We do sometimes want a piece of code to be run when our Angular app is starting, maybe load some settings, load cache, load configurations or do some check-ins. The AppInitialzer token helps out with that.

APP_INITIALIZER: A function that will be executed when an application is initialized.

It is easy to use. Let’s we want this runSettings function to be executed on our Angular app startup:

``` bash
function runSettingsOnInit() {
    ...
}
```

``` bash
@NgModule({

    providers: [
        { provide: APP_INITIALIZER, useFactory: runSettingsOnInit }
    ]

})
```

Just like AppInitializer, Angular has a feature that enables us to listen on when a component is being bootstrapped. It is the APP_BOOTSTRAP_LISTENER.

## DOCUMENT

``` bash
@Component({
})
export class CanvasElement {
    constructor(@Inject(DOCUMENT) _doc: Document) {}
    renderCanvas() {
        this._doc.getElementById("canvas")
    }
}
```

Warning: Tread carefully! Interacting with the DOM directly is dangerous and can introduce XSS risks.

## NgPlural

``` bash
<p [ngPlural]="components">

    <ng-template ngPluralCase="=1">1 component removed</ng-template>    
    <ng-template ngPluralCase=">1">{{components}} components removed </ng-template>    

</p>
```

To use this directive you must provide a container element that sets the [ngPlural] attribute to a switch expression. Inner elements with a [ngPluralCase] will display based on their expression

## Bootstrap Listener

``` bash
@NgModule({
    {
        provide: APP_BOOTSTRAP_LISTENER, multi: true, 
        useExisting: runOnBootstrap
    }
    ...
})
export class AppModule {}
```

To use APP_BOOTSTRAP_LISTENER, we add it to the providers section in our AppModule with the callback function

#### More info: [original link](https://blog.bitsrc.io/10-useful-angular-features-youve-probably-never-used-e9e33f5c35a7)

