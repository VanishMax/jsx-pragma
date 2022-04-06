###

<img align="right" src="./public/readme-icon.svg" />
<img src="./public/readme-title.svg" />

## 


The app is a proof-of-concept of how you can use the JSX in vanilla TypeScript + Vite project, no framework needed!

```tsx
import { h } from '../pragma';
import { LikeComponent } from './like';

const App = (
  <main className="hello">
    <h1>
      Hello JSX!
    </h1>
    <LikeComponent big />
  </main>
);

export default App;
```

This project is for you, if you:

* ⚛️ Have experience with React, but no idea about how it handles JSX
* 🕵️‍♂️ Curious about front-end fundamentals
* 🤓 A geek who loves vanilla TypeScript and all around it

## So how does it work?

**JSX** is basically a syntactic extension over JS, and, just like TypeScript, it needs to be compiled.

![JSX Code transformation: from code to typescript parser, vite bundler, into the Vanilla JS code](public/flowchart.png)

The flowchart shows that, to get the vanilla JS code from JSX, we need to instruct the TypeScript or Vite (in case the project is without TS) compilers to parse the JSX and transform it to the required form. So, what instructions do they need? Well, all the TypeScript need (in `tsconfig.json`) is:

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxFactory": "h",
    "jsxFragmentFactory": "Fragment"
  }
}
```

And Vite requires the following `vite.config.ts`:

```ts
import { defineConfig } from 'vite';

export default defineConfig({
  esbuild: {
    jsxFactory: 'h',
    jsxFragment: 'Fragment'
  }
});
```

These instructions tell the compilers to use the `h` and `Fragment` functions to work with your JSX. Compilers parse the `.jsx` or `.tsx` files, produce their own representation of the data in JSX and feed this data to the `h` function.

The `h` function is called the **JSX Pragma**. In React, the pragma is the `React.createElement(component, props, ...children)` function, which is clearly described in [React docs](https://reactjs.org/docs/jsx-in-depth.html). In our case, though, `h` is written by ourselves in `src/pragma.ts` file.

```ts
type Tag = string | ((props: any, children: any[]) => JSX.Element);
type Props = Record<string, string | number | null | undefined> | null;
type Children = (Node | string)[];

export const h = (tag: Tag, props: Props, ...children: Children) => {
  // If the tag is a function component, pass props and children inside it
  if (typeof tag === 'function') {
    return tag({ ... props }, children);
  }

  // Create the element and add attributes to it
  const el = document.createElement(tag);
  if (props) {
    Object.entries(props).forEach(([key, val]) => {
      if (key === 'className') {
        el.classList.add(...(val as string || '').trim().split(' '));
        return;
      }

      (el as any)[key as keyof HTMLElement] = val;
    });
  }

  // Append all children to the element
  children.forEach((child) => {
    el.append(child);
  });

  return el;
};
```

As you can see, all the Pragma does is goes through the parsed JSX and creates DOM elements with correct attributes and children. This allows the magic to happen when you import the `h` to your functional components.

This is it! You are good to go into the JSX world! If you are interested, I have **a task for you**: create a `CounterComponent` that simply displays decrease and increase buttons and the counter itself. It might teach you the principles of reactivity 🏎

## How to run?

Firstly, install the dependencies:

```bash
npm install
```

And start the application:

```bash
npm run dev
```

It will bind the app to `http://localhost:3000`.

## Copyright

Licensed by the terms of [the MIT license](./LICENSE)
