---
title: "Unsafe or Low-Level? Why Rust’s unsafe Misses the Mark"
date: 2025-05-20T11:00:00Z
categories: ["programming", "C", "Zig", "Rust", "Go"]
draft: false
slug: "unsafe-rust-misses-the-mark"
---

## The Intention Behind `unsafe`

Rust’s `unsafe` keyword separates code its safety model can guarantee from code it can’t guarantee, but as a consequence it falsely frames low-level code as unsafe, and this has language design implications.

## The Problem with Framing Low-level Code as Unsafe

Rust's framing of low-level code as unsafe conceptually sidelines low-level semantics, checks and tooling. It makes writing incorrect high-level code hard, but doesn't offer the same degree of rigor for low-level code. And even for high-level code this approach feels backwards: why make writing incorrect code hard, when you can make writing correct code easy?


Rust treats low-level control as a rare, expert-only tool, but aren't software developers supposed to be experts? Rust asserts that "unsafe" (low-level) code should be written by library authors, and exposed to developers through safe APIs, but who exactly is the intended audience here, if not expert software developers? The low-level domain isn't confined to writing libraries.


Low-level control is a core feature of systems programming, and developers deserve robust semantics, checks and tooling for it.


I can't be the only one who finds it slightly ironic that "unsafe" Rust code can be perfectly safe, and "safe" Rust code can have critical non-memory related bugs. And not only that, but if there is a bug in the unsafe code that comprises the safe wrapper, suddenly your safe code isn't memory safe either.


Better low-level safety would strengthen the entire ecosystem.


Maybe if the keyword were changed to `lowlevel` it would make more sense, but even then the low-level safety features would be lacking because of how the language has been designed around `unsafe`: at every point, it has been emphasized that it's not `lowlevel` code, it's `unsafe`.


I would be completely fine with separating low-level code from high-level code, so long as the keyword used for it didn't serve as a conceptual roadblock for innovating low-level safety semantics, granular checks, and integrated tooling. 

## Zig's Promise

Zig, for example, has low-level semantics that make writing correct code easy: optionals, error unions, explicit allocators (a godsend!), and defer. Its build system integrates granular compile-time and runtime checks with robust tooling: it's like having gcc, asan, make, static analysis, code formatting, unit testing, and more, all in one place. 


None of this makes programming more difficult, complicates the language model, or increases compile times sevenfold. Instead, it makes writing correct code easier at every level of abstraction.


This reflects the origins and priorities of these languages, with Zig being designed by C devs reimagining C, and Rust being designed by C++ devs reimagining C++.


The developers who designed Rust were right that understanding everything in C++ is impossible, and so they naturally felt the need to distinguish between experts and non-experts, but in a language like C or Zig, it's not only feasible for a developer to have a complete understanding of the language, it's expected.


With Zig I think that, with its low-level ergonomics, it will have a similar level of practical safety to Rust. Even if it's only 85% as safe, it will still certainly be safer than C, without headaches of either language.

## Why C and Not Rust (Or Even Zig)?

If large codebases like Linux, PostgreSQL, Git, Curl, Nginx, Redis, and so on, can be robust, then I'm perfectly happy to continue using C until another language (maybe Zig) comes around and gets things right. When I want to do something high-level, Go is a nice choice (and I can still write C code and import it into Go). I'd love to use Zig, but it's still growing and I don't want to have to deal with the breaking API changes and lack of stable documentation. 


At the end of the day, a programming language is a tool, and I prefer tools that simplify my work over ones than complicate it. While I appreciate the intentions behind Rust, and even its practical results, I don't think it effectively addresses the needs of systems programmers. And I think its approach to safety is flawed for the domain of systems programming.

## A Tool Should Make My Job Easier, Not Harder

A language for professional software developers should make writing correct code easier at every level of abstraction, and it should so simply. It shouldn't just make it harder to write incorrect code at only one level of abstraction. You should not have to fight with a tool: a tool should make your job easier.

