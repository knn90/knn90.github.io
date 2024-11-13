---
title: "[iOS] Handle deep link with chain responsibility pattern"
date: 2023-08-02T12:24:21+07:00
draft: false
tags: ["design-pattern", "swift"]
categories: ["ios"]
---

The idea of deep linking is simple. You have an URL click to open your application and navigate to a specific screen. However, it can be messy when it comes to code, and you may end up with something like this.

```Swift
    if url.absoluteString.contains("/login") {
        // Navigate to Login screen
    } else if url.absoluteString.contains("/products") {
        // Navigate to Products screen
    } else if url.absoluteString.contains("/payment") {
        // Navigate to Payment screen
    } else {
        // Navigate to Home screen
    }
```

You can imagine what happens if you have ten types of URLs. This design also violates the **Open/Closed Principle**, and every time you add a new URL, you will have to open this file and add one more if-else block. Now is the solution.

![chain_responsibility](/images/ios/chain_responsibility.png)

Let me briefly explain what each class does, the details will come later.

The `Registry` class will hold an array of `DeepLinkHandler`.

The `DeepLinkHandler` is a protocol with two functions

- `canHandle`: take in an URL and return a Bool to determine the ability to handle the URL
- `handle`: The action when the application receives the URL

When the application is started, all the `DeeplinkHandlers` are composed and registered to the `Registry` object.

```Swift
    init() {
        registry = Registry(handlers: [
            LoginDeepLinkHandler(),
            ProductsDeepLinkHandler(),
            PaymentDeepLinkHandler(),
            HomeDeepLinkHandler()
        ])
    }
```

And when receiving an URL, the `Registry` will handle it.

```Swift
    WindowGroup {
        ContentView()
            .onOpenURL { url in
                registry.handle(url)
            }
    }
```

Inside the handle function, we will iterate through the handlers array to find the first handler that can handle the URL and pass the message forward.

```Swift
    class Registry {
        private let handlers: [DeepLinkHandler]

        init(handlers: [DeepLinkHandler]) {
            self.handlers = handlers
        }

        func handle(_ url: URL) {
            handlers.first(where: {
                $0.canHandle(url: url)
            })?.handle()
        }
    }
```

You can add a fallback handler at the end of the handlers array for a better user experience if the URL is not registered yet, for example, a “Can’t handle URL” error or maybe a WebView.

In this case, the `HomeDeepLinkHandler` will be the last one to handle the URL if no one else can.

That’s pretty simple, right!?

So, where is the chain of responsibility pattern? It’s the iteration in the Registry class. Only the first handler that can handle the URL will do the job. Just like when you tap on the screen, the first view that can process the tap will trigger its action.

Finally, you don’t need to modify the code when adding a new type of URL type anymore. So that the `Open/Closed Principle` violation is resolved.

You can find the source code [here](https://github.com/knn90/Deeplink_Chain_Responsibility)

Thanks to Caio and Mike from EssentialDevelop.com, I learn this design from their talk.

Cheers!!!
