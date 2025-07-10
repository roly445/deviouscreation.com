+++
date = '2025-07-10T13:45:16+01:00'
draft = false
title = 'Deep Dive: CQRS Architecture Made Simple with BluQube'
summary = 'Explore how BluQube simplifies the CQRS pattern in Blazor apps, letting you clearly separate commands and queries, reduce boilerplate, and write scalable business logic that runs seamlessly on both Server and WASM.'
+++

**BluQube Blog Series:**

1. [The Problem: Why Blazor Developers Need BluQube](/posts/the-problem-why-blazor-developers-need-bluqube)
2. [Introducing BluQube: Write Once, Run Everywhere in Blazor](posts/introducing-bluqube-write-once-run-everywhere-in-blazor/)
3. *Deep Dive: CQRS Architecture Made Simple with BluQube*
4. Source Generation Magic: How BluQube Automates the Boring Stuff
5. Building Real Applications: A Todo App Case Study
6. Validation, Error Handling, and Authorisation in BluQube
7. Lessons Learnt: What I Discovered Building BluQube

---

In the [previous article](link-to-article-2), I introduced BluQube and explained how it enables you to write your business logic once and run it anywhere in Blazor, whether you choose Server or WASM. In this post, we’ll go deeper into the core architectural pattern that makes this possible: **Command Query Responsibility Segregation**—better known as CQRS.

## What is CQRS, and Why Does It Matter?

CQRS stands for Command Query Responsibility Segregation. In simple terms, it means splitting your application logic into two distinct categories:

- **Commands**: Operations that change state (e.g., creating a todo, updating a user profile).
- **Queries**: Operations that retrieve data (e.g., fetching a list of todos).

Traditionally, many applications mix these responsibilities together in the same service or component. This quickly leads to tangled code, makes testing difficult, and can cause subtle bugs—especially as your app grows.

CQRS offers several benefits:

- **Clarity**: It’s always clear if a piece of logic is supposed to read or write data.
- **Testability**: Commands and queries can be tested in isolation.
- **Scalability**: Reading and writing can scale independently, even if your app grows.
- **Security**: Validation and authorisation are easier to reason about when separated.

BluQube embraces CQRS at its core, making these advantages available to every Blazor app—without you having to build your own abstractions.

## CQRS in BluQube: The Basics

BluQube codifies CQRS into simple, repeatable patterns. Here’s how it works:

### 1. Define Your Commands and Queries

Commands and queries in BluQube are just C# records, decorated with attributes to describe their intent and routing.

```csharp
// A command that adds a new todo item
[BluQubeCommand(Path = "commands/addtodo")]
public record AddTodoCommand(string Title) : ICommand;

// A query that fetches all todo items
[BluQubeQuery(Path = "queries/gettodos")]
public record GetTodosQuery() : IQuery<GetTodosResult>;
```

### 2. Implement Handlers

Business logic lives in handler classes. Command handlers inherit from `CommandHandler<TCommand>`, giving you built-in validation and structured results. Query handlers implement `IQueryHandler<TQuery, TResult>`.

```csharp
public class AddTodoHandler : CommandHandler<AddTodoCommand>
{
    protected override Task<CommandResult> HandleInternal(AddTodoCommand command, CancellationToken cancellationToken)
    {
        // Add the todo (your business logic)
        return Task.FromResult(CommandResult.Success());
    }
}

public class GetTodosHandler : IQueryHandler<GetTodosQuery, GetTodosResult>
{
    public override Task<QueryResult<GetTodosResult>> HandleInternal(GetTodosQuery query, CancellationToken cancellationToken)
    {
        // Fetch todos (your business logic)
        return Task.FromResult(QueryResult<GetTodosResult>.Succeeded(new GetTodosResult(...)));
    }
}
```

### 3. Dispatch from Anywhere—Server or WASM

Using BluQube, you can send commands and queries from your Blazor components. BluQube takes care of the plumbing—routing to the correct environment, serialising data, handling validation, and returning results.

```csharp
// In a Blazor component
await _commander.Send(new AddTodoCommand("Buy milk"));
var todos = await _commander.Query(new GetTodosQuery());
```

## How BluQube Makes CQRS Simple

CQRS can be intimidating for newcomers, but BluQube removes the boilerplate and complexity. Here’s how:

- **Automatic Routing**: Whether on Server or WASM, BluQube routes commands and queries to the right place.
- **Source Generation**: Handlers are wired up automatically, so you don’t need to register them by hand.
- **Validation & Error Handling**: Commands are validated using FluentValidation, and errors come back in a consistent format.
- **Minimal Boilerplate**: Define intent, write your logic, and you’re done.

## Why Not Just Use MediatR or Another CQRS Library?

MediatR and similar libraries are great for server-side .NET applications, but they aren’t optimised for Blazor’s dual-environment needs. BluQube is built for Blazor from the ground up, ensuring your logic runs seamlessly in both Server and WASM projects—with no code duplication or awkward workarounds.

## Example: Adding and Listing Todos (CQRS in Action)

Let’s see CQRS in BluQube with a concrete example. Here’s how you might implement a todo feature:

```csharp
// Command - add a todo
[BluQubeCommand(Path = "commands/addtodo")]
public record AddTodoCommand(string Title) : ICommand;

// Handler for the command
public class AddTodoHandler : CommandHandler<AddTodoCommand>
{
    protected override Task<CommandResult> HandleInternal(AddTodoCommand command, CancellationToken cancellationToken)
    {
        // Save the todo to a repository or database
        return Task.FromResult(CommandResult.Success());
    }
}

// Query - get all todos
[BluQubeQuery(Path = "queries/gettodos")]
public record GetTodosQuery() : IQuery<GetTodosResult>;

// Handler for the query
public class GetTodosHandler : IQueryHandler<GetTodosQuery, GetTodosResult>
{
    public override Task<QueryResult<GetTodosResult>> HandleInternal(GetTodosQuery query, CancellationToken cancellationToken)
    {
        var todos = ... // Load todos from repository
        return Task.FromResult(QueryResult<GetTodosResult>.Succeeded(new GetTodosResult(todos)));
    }
}
```

Now, your Blazor components can use these commands and queries without worrying about which environment they’re running in.

## The BluQube Advantage

BluQube delivers the power of CQRS while abstracting the headaches. You get:

- A clean separation of concerns
- Built-in validation and error handling
- Native Blazor support (Server & WASM)
- Source-generated boilerplate reduction
- A scalable, future-proof architecture

## Up Next

In the next article, we’ll look at how BluQube uses source generation to automate repetitive tasks, improve performance, and keep your codebase clean.

---

**Want to try it yourself?** Explore [BluQube on GitHub](https://github.com/roly445/bluqube) for full examples and documentation.
