---
title: "[iOS] Enhance User Experience with Smooth Transitions in SwiftUI using AnimatedToastView"
date: 2024-11-19T19:18:21+07:00
draft: false
tags: ["SwiftUI", "swift", "animation"]
categories: ["ios"]
---

AnimatedToastView enhances SwiftUI applications by automating view transitions with smooth animations. By customizing the duration and leveraging various transition effects.

## Code Overview

Here's the implementation and usage of `AnimatedToastView`:

```swift
struct AnimatedToastView<Content: View>: View {
    @State private var isVisible: Bool = false
    private let content: () -> Content
    private let displayDuration: CGFloat

    init(displayDuration: CGFloat, @ViewBuilder content: @escaping () -> Content) {
        self.displayDuration = displayDuration
        self.content = content
    }

    var body: some View {
        VStack {
            if isVisible {
                content()
            }
        }
        .task {
            withAnimation(.easeInOut) {
                isVisible = true
            }
            Timer.scheduledTimer(
                withTimeInterval: TimeInterval(displayDuration),
                repeats: false) { _ in
                    withAnimation(.easeInOut) {
                        isVisible = false
                    }
                }
        }
    }
}
```

## Understanding the Structure

- **State Management**: The `@State` property `isVisible` determines when the content should be displayed, automatically triggering animations when it changes.
- **Initializer**: Accepts a `displayDuration` to define how long the content should stay visible before being animated out of view, and a `content` view which will be animated.

- **Task Modifier**: Used to schedule the animations:
  - Initially animates `isVisible` to `true`, making the content appear with a smooth animation.
  - A `Timer` is used to handle the automatic disappearance of the view after the specified duration.

## Preview

The animation for showing/hiding the content of `AnimatedToastView` is fully customizable.

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 16) {
            ZStack {
                background
                AnimatedToastView(displayDuration: 2.0) {
                    sampleText
                }
            }.frame(height: 100)

            HStack {
                background
                AnimatedToastView(displayDuration: 3.0) {
                    sampleText
                        .transition(.move(edge: .trailing))
                }
            }.frame(height: 100)

            HStack {
                AnimatedToastView(displayDuration: 3.0) {
                    sampleText
                        .transition(.move(edge: .leading))
                }
                background
            }.frame(height: 100)

            VStack() {
                background
                AnimatedToastView(displayDuration: 4.0) {
                    sampleText
                        .transition(.move(edge: .bottom))
                }
            }.frame(height: 100)
        }
        .padding()
    }

    @ViewBuilder
    private var background: some View {
        LinearGradient(
            gradient: Gradient(colors: [Color.blue.opacity(0.3), Color.purple.opacity(0.3)]),
            startPoint: .top,
            endPoint: .bottom
        )
    }

    @ViewBuilder
    private var sampleText: some View {
        Text("Welcome to SwiftUI!")
            .font(.headline)
            .padding()
            .background(Color.green)
            .cornerRadius(12)
            .shadow(radius: 5)
    }
}
```

![AnimatedToastView_preview](/images/ios/animatedToast_preview.gif)

You can find the source code [here](https://github.com/knn90/AnimatedToastView.git)

### Happy coding!
