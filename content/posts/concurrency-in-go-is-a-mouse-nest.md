---
title: "Concurrency in Go is a Mouse Nest"
date: 2025-05-22T11:00:00Z
categories: ["programming", "Go", "concurrency"]
draft: false
slug: "concurrency-in-go-is-a-mouse-nest"
---

## The Mouse Nest is the Program

Imagine a mouse nest where mice cooperate for maximum productivity. Just as the nest is the central hub where all mouse activities are coordinated, in Go the program is the environment where all concurrent operations take place.

## Forager and Burrow Mice are Goroutines

Within the nest, there are forager mice who gather food, nesting materials, or other resources, and burrow mice who store and organize those resources in the nest. There are also hybrid mice who take on both roles simultaneously, however, hybrid mice aren't as common because they can complicate coordination. In Go, these mice represent goroutines, which are lightweight threads that perform specific tasks, either sending data (like forager mice), receiving data (like burrow mice), or sending and receiving data (like hybrid mice).

## Entrances are Typed Channels

To avoid congestion, the nest has dedicated entrances: one for food, one for nesting materials, etc. In Go, these entrances are channels, and they are typed, meaning each channel handles a specific type of data (e.g., food or nesting materials). Though channels are bi-directional, they’re typically not passed as such. Channels are mostly passed as either send-only or receive-only in order to clearly define the goroutine’s role, but sometimes sender-receiever goroutines are necessary.

## Coordination and Blocking

At each entrance, coordination happens. For example, from the food entrance, forager mice go out to collect food, and burrow mice wait to store and organize the dropped-off food. If a burrow mouse is busy organizing resources, the forager mouse waits until the burrow mouse returns before going out to forage again. And if a forager mouse is busy foraging, the burrow mouse waits until the forager mouse returns before going back in to organize. In Go, this represents unbuffered channels, where a sender (forager mouse) blocks until a receiver (burrow mouse) is ready to take the data.

## Nooks are Buffers

Some entrances have a nook to store a finite amount of resources, allowing forager mice to drop off resources and leave without waiting for a burrow mouse unless the nook is full. In Go, this represents buffered channels, which reduce blocking in high traffic scenarios by allowing senders to send data up to the buffer’s capacity without waiting for a receiver.


## Many Mice, Many Entrances

If a single forager mouse arrives at an entrance, the first available burrow mouse takes the resource from him, but if multiple forager mice arrive simultaneously, the burrow mouse grabs from one arbitrarily to keep things flowing. Multiple forager mice can collect various resources (e.g., seeds, berries, feathers, twigs), and multiple burrow mice can handle those resources at each entrance. The nest can support hundreds of mice working simultaneously across many entrances. In Go, multiple senders and receivers can operate independently across channels in a safe, synchronized, and efficient way. Goroutines are lightweight and capable of scaling efficiently across many channels.

## Flexible Coordination with Select

Some forager and burrow mice flexibly coordinate between entrances. A burrow mouse might stand between entrances, grabbing resources from whichever forager mouse arrives first. Similarly, a forager mouse might gather different resources and deliver them to whichever entrance is needed. If no forager mice are at the entrances, these flexible burrow mice might default to organizing existing resources in the nest; and if no burrow mice are at the entrances, these flexible forager mice might default to assessing what resources the nest needs most. In Go, this flexibility is achieved with select statements, which allow senders and receivers to handle data from multiple channels, blocking if no data is ready unless a default case specifies an action to take instead of blocking.

## Cleaning Up and Closing Entrances

When the forager mice finish foraging for the day, one of them closes the entrance with nesting material, signaling that no more of those resources will come. Burrow mice can still collect the last dropped-off resource and any remaining resources in the nook (if there is one), but if any forager mice arrive at the closed entrance, they can't utilize it anymore. In Go, when a channel is closed, no new data can be sent through it, but receivers can still access the last sent data and any data remaining in the buffer.

Before closing, the mice need to clean up stray resources like seeds or twigs outside the entrance so nothing is wasted and no clutter attracts predators. In Go, goroutines must clean up resources like memory, file handles, or connections, using synchronization primitives, before closing. 

## The Sentry Mouse and Context Cancellation

There is actually one other type of mouse in the nest: the sentry mouse. The sentry mouse sits up on a branch above the nest and watches for predators and changing weather. When he spots danger, he squeaks a warning, signaling all the other mice to finish up what they're doing and get inside the nest. The other mice finish dropping off or organizing their last resource, clean up what they can, close the entrances, and head inside. In Go, this is context cancellation: a signal tells goroutines to stop, often due to timeouts or user requests. Goroutines check for this signal in a select statement, clean up and exit.

## Deadlocks and Dependency

Each entrance requires at least one forager mouse and one burrow mouse. Without a burrow mouse, forager mice end up filling the nooks and waiting at the entrance to hand off resources; and without a forager mouse, burrow mice end up waiting at the entrance to receive resources. If all mice are busy or at the wrong entrance, productivity halts. In Go, each channel requires at least one sender and one receiver. If goroutines are blocked waiting for each other, a deadlock occurs, and if all goroutines are blocked, the Go runtime panics.

