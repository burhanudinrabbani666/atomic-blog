# Optimizing Bundle Size

## Understanding the Bundle

**SERVER** --on initial load--> **CLIENT**

- **Bundle**: A JavaScript file containing your entire application code. When downloaded, it loads the entire app at once, turning it into a Single Page Application (SPA)
- **Bundle size**: The amount of JavaScript users must download before they can start using your app. This is one of the most critical performance metrics to optimize
- **Code splitting**: Dividing the bundle into multiple smaller parts that can be downloaded on demand ("lazy loading")

## Why Optimize Bundle Size?

Smaller bundles mean:

- Faster initial page load
- Better user experience, especially on slower networks
- Reduced data usage for mobile users

## Implementing Lazy Loading with React

### Traditional Import (Static)

Usually, you import components with a static import statement:

```jsx
import MarkdownPreview from "./MarkdownPreview.js";
```

This loads the component immediately when the app starts, even if the user never visits that page.

### Lazy Import (Dynamic)

To defer loading a component's code until it's actually needed, use React's `lazy` function:

```jsx
import { lazy } from "react";

const MarkdownPreview = lazy(() => import("./MarkdownPreview.js"));
```

**Important requirements:**

- The lazy component must be exported as a **default export**
- Your bundler or framework must support dynamic `import()`

### Adding a Loading Fallback

Lazy-loaded components need a fallback UI while they're loading. Wrap them in a `<Suspense>` boundary:

```jsx
import { Suspense, lazy } from "react";

const MarkdownPreview = lazy(() => import("./MarkdownPreview.js"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <MarkdownPreview />
    </Suspense>
  );
}
```

## Real-World Example

Lazy load route components to split your app by page:

```jsx
import { lazy, Suspense } from "react";

const Homepage = lazy(() => import("./pages/Homepage"));
const Product = lazy(() => import("./pages/Product"));
const Pricing = lazy(() => import("./pages/Pricing"));
const PageNotFound = lazy(() => import("./pages/PageNotFound"));
const AppLayout = lazy(() => import("./pages/AppLayout"));
const Login = lazy(() => import("./pages/Login"));

function App() {
  return (
    <Suspense fallback={<SpinnerFullPage />}>
      <Routes>
        <Route path="/" element={<Homepage />} />
        <Route path="/product" element={<Product />} />
        <Route path="/pricing" element={<Pricing />} />
        {/* ... other routes */}
      </Routes>
    </Suspense>
  );
}
```

## Best Practices

- Lazy load route-level components first (biggest impact)
- Lazy load heavy components that aren't immediately visible
- Don't lazy load small components (the overhead isn't worth it)
- Provide meaningful loading states in `<Suspense>` fallbacks

---

[Next: Don't optimize prematurely](./11-dont-opimize-prematury.md)
