+++
date = '2025-06-23T17:08:15+01:00'
draft = false
title = 'Introducing Bluqube Write Once Run Everywhere in Blazor'
summary = 'Discover how BluQube lets you write your business logic once and run it seamlessly on both Blazor Server and WebAssembly. This article introduces the core concepts behind BluQube, its CQRS-based approach, and how it tackles code duplication, validation, and deployment headaches for Blazor developers.'
+++

**BluQube Blog Series:**

1. [The Problem: Why Blazor Developers Need BluQube](/posts/the-problem-why-blazor-developers-need-bluqube)
2. Introducing BluQube: Write Once, Run Everywhere in Blazor
3. Deep Dive: CQRS Architecture Made Simple with BluQube
4. Source Generation Magic: How BluQube Automates the Boring Stuff
5. Building Real Applications: A Todo App Case Study
6. Validation, Error Handling, and Authorisation in BluQube
7. Lessons Learnt: What I Discovered Building BluQube

---

*In the [previous article](/posts/the-problem-why-blazor-developers-need-bluqube), I described the challenges faced by Blazor developers: the “write-twice” dilemma of choosing between Blazor Server and Blazor WebAssembly (WASM) and the resulting code duplication and complexity. Today, I’m excited to introduce you to BluQube—a framework I built to solve exactly this problem.*

---

## What is BluQube?

[BluQube](https://github.com/roly445/bluqube) is a framework for Blazor that enables you to write your business logic once and run it seamlessly on both Blazor Server and Blazor WASM. It abstracts away the differences between deployment models, so you can concentrate on building features, not duplicating code.

### Key Features at a Glance

- **Unified Blazor Development:** Write application logic once, deploy on Server or WASM.
- **CQRS Pattern:** Clean command and query separation, inspired by the Command Query Responsibility Segregation (CQRS) architecture.
- **Integrated Validation:** Built-in, detailed validation for commands using FluentValidation.
- **Source Generation:** Automated code generation for optimal performance and maintainability.
- **Comprehensive Error Handling:** Structured error data and clear reporting.
- **Authorisation Support:** Declarative, policy-based authorisation for commands and queries.

## How Does BluQube Work?

The core idea of BluQube is to standardise the way you write and handle commands (actions that change state) and queries (actions that fetch data) in your Blazor apps. This means your business logic isn’t tied to either the server or the client—it’s portable and reusable.

### The BluQube Command & Query Pattern

At the heart of BluQube is a simple pattern:

1. **Define your intent:** Create commands and queries as plain C# records.
2. **Implement handlers:** Write business logic in handler classes—note that command handlers inherit from `CommandHandler`, which provides validation handling, while query handlers do not include validation.
3. **Send and process:** Use BluQube’s abstractions to send commands and queries from any Blazor component, on any platform.

#### Example: A Simple Command

```csharp
// Define a command
[BluQubeCommand(Path = "commands/addtodo")]
public record AddTodoCommand(string Title) : ICommand;

// Implement the handler
public class AddTodoHandler : CommandHandler<AddTodoCommand>
{
    protected override Task<CommandResult> HandleInternal(AddTodoCommand command, CancellationToken cancellationToken)
    {
        // Add the todo item (business logic)
        // Save to database or in-memory store
        return Task.FromResult(CommandResult.Success());
    }
}
```
*Note: Command handlers inherit from `CommandHandler`, which provides validation support out of the box.*

#### Example: A Query

```csharp
// Define a query
[BluQubeQuery(Path = "queries/gettodos")]
public record GetTodosQuery() : IQuery<GetTodosResult>;

public record GetTodosResult(List<TodoItem> Items) : IQueryResult;

// Implement the handler
public class GetTodosHandler : IQueryHandler<GetTodosQuery, GetTodosResult>
{
    public override Task<QueryResult<GetTodosResult>> HandleInternal(GetTodosQuery query, CancellationToken cancellationToken)
    {
        var todos = // fetch todos from store
        return Task.FromResult(QueryResult<GetTodosResult>.Succeeded(new GetTodosResult(todos)));
    }
}
```
*Note: Queries in BluQube do not perform validation.*

### One Business Logic, Multiple Platforms

Whether you run your app on Blazor Server or WASM, BluQube ensures the same command/query logic is used. There’s no need to rewrite or wrap code for each deployment model. You maintain a single source of truth.

## Why CQRS?

CQRS (Command Query Responsibility Segregation) is a pattern that separates read and write logic. In BluQube, this means you define:

- **Commands:** For operations that change data (e.g., AddTodoCommand)
- **Queries:** For operations that retrieve data (e.g., GetTodosQuery)

This separation brings clarity to your codebase, enabling easier testing, better scalability, and clearer intent.

## Built-in Validation and Error Handling

BluQube integrates [FluentValidation](https://github.com/FluentValidation/FluentValidation) for commands, offering comprehensive validation and detailed error feedback out of the box. (Queries are intentionally not validated.) Additionally, errors are handled uniformly, providing structured error data that you can use to display meaningful messages to users.

## Source Generation for Performance

BluQube uses source generation to automate repetitive tasks and optimise performance. This means less boilerplate for you and faster execution for your users.

## Getting Started

If you’re ready to try BluQube, installation is simple:

```bash
dotnet add package BluQube
```

Have a look at the [GitHub repository](https://github.com/roly445/bluqube) for setup instructions, examples, and the complete source code.

## What’s Next?

In the next article, we’ll take a deeper dive into the CQRS architecture used in BluQube, and see how this pattern makes your Blazor apps more scalable, testable, and maintainable.

---

Ready to stop writing everything twice and start building faster? [Explore BluQube on GitHub!](https://github.com/roly445/bluqube)