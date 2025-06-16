---
author: Angel Escalante
pubDatetime: 2025-06-16
title: A beginner's cheat sheet for TypeScript
slug: typescript-cheat-sheet
featured: false
draft: false
tags:
  - TypeScript
  - JavaScript
  - Web Development
description: A beginner-friendly cheat sheet to understand the essentials of TypeScript. Perfect for JavaScript developers who want to level up.
---

For a long time, I stayed away from TypeScript. I thought it was too verbose, too strict, or just something that added friction to my workflow. But once I gave it a real shot, I realized how much peace of mind it brings when working on bigger web projects.

This post is a quick primer for those who want to get started with TypeScript but don't want to read through the whole manual. 📘 Instead, this cheat sheet highlights the essentials that helped me understand its mindset and syntax.

## What TypeScript _is_ (and what it’s not)

Before jumping in, let’s clarify a few things.

✅ **TypeScript is**:

- A superset of JavaScript.
- A way to add **optional static types** to your code.
- A tool that helps you **catch bugs at compile time**.
- Totally compatible with modern JavaScript (ES6+).
- Highly configurable — you can go from “light typing” to “strict mode” depending on your needs.

❌ **TypeScript is not**:

- A different language — your TS code compiles down to plain JavaScript.
- A replacement for JavaScript — browsers don’t understand `.ts` files, so you still ship JS.
- Just for big projects — it works great even in small codebases.

---

## 🧠 Cheat Sheet: TypeScript Essentials

Here’s a quick reference to the things I use when writing TypeScript.

### ✨ Basic Types

```ts
let isDone: boolean = false;
let age: number = 33;
let username: string = "angel";
let tags: string[] = ["astro", "typescript"];
let anything: any = "you can assign anything here";
```

### 🧱 Tuples and Enums

```ts
let user: [string, number] = ["Angel", 33];

enum Status {
  Loading,
  Success,
  Error,
}

let current: Status = Status.Loading;
```

### 🔍 Type Aliases and Interfaces

```ts
type User = {
  name: string;
  age: number;
};

interface Product {
  id: number;
  name: string;
  price?: number; // optional
}
```

Both type and interface can be used for declaring object shapes. In general:

- Use interface when defining shapes of objects/classes
- Use type when you need unions, intersections, or more flexibility

### 🧰 Functions with Types

```ts
function greet(name: string): string {
  return `Hello, ${name}`;
}

const sum = (a: number, b: number): number => a + b;
```

### 🧱 Union and Literal Types

```ts
let status: "loading" | "success" | "error";
status = "loading"; // ✅
status = "done"; // ❌ Error

type ID = string | number;
let userId: ID = 123;
```

### 🏗️ Type Narrowing

```ts
function handleInput(input: string | number) {
  if (typeof input === "string") {
    console.log("String input:", input);
  } else {
    console.log("Number input:", input);
  }
}
```

### 🧩 Generics

```ts
function identity<T>(arg: T): T {
  return arg;
}
const str = identity<string>("Hello");
const num = identity<number>(42);
```

### 🦺 Type Guards and in

```ts
type Dog = { bark: () => void };
type Cat = { meow: () => void };

function isDog(animal: Dog | Cat): animal is Dog {
  return "bark" in animal;
}
```

Happy typing! ⌨️✨
