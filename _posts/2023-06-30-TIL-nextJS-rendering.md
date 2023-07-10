---
title: "Next.js Server Components vs. Client Components"
layout: splash
read_time: true
header:
  overlay_image: assets/images/githubIo.png
categories:
- TIL
last_modified_at: 2023-06-30T09:45:06-05:00
---

# Next.js Server Components vs. Client Components

If you are like me, who uses Vue but new to Next.js and React, the whole concept of server component and client component can be confusing. This post is more of a note to myself to help me understand the concept of Next.js rendering.

## New component level rendering
In the recent release of Next.js 13, they introduced the concept of hybrid rendering. Hybrid rendering is the combination of server-side rendering and client-side rendering. In Next.js, you can choose to render your components on the server or on the client. 

By splitting the rendering technique, you can now selectively choose which components to render on the server and which components to render on the client. This can be powerful in terms of performance compared to the traditional server rendering.

![img.png](/assets/images/nextjs-component.png)
*Image from [Next.js Docs](https://nextjs.org/docs/getting-started/react-essentials)*

## When to use what
So okay, I understand that we can mix and match and it'd be amazing. But when do we use what? Before answering that question, let's first understand the difference between server components and client components.

### Server Components
As the component gets build when the page is requested for server components, it is not possible to use any browser APIs. However, you can use Node.js APIs. This means the initial page load is faster. If you have `console.log` in your server component, you will see the log in your terminal, not in your browser console.
Also, note that you can't use any React hooks in your server components. in the `app` directory, default behavior is a server component.

**Sever Component (default behavior)**
- Faster initial page load
- Can't use browser APIs
- Can't use React hooks
- Can use Node.js APIs
- Better for Data Fetching

### Client Components
If you want to use client component, you need to add `use client` to your component. According to the docs, `use client` sits between server-only and client code. So even client component will be rendered on the server, but it will be hydrated on the client. This means that the interactive part of the component will be rendered on the client. 


**Client Component (add `use client`)**
- For interactive components
- Can use browser APIs
- Can use React hooks
