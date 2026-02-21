# Security Policy

## Supported Versions

| Version | Supported          |
|---------|--------------------|
| 1.0.x   | Yes                |
| < 1.0   | No                 |

## Important Notice

microMVC+ is a **client-side JavaScript framework** that runs entirely in the browser.

This has critical security implications that every developer using this library must understand.

### What microMVC+ does not protect against

**Never run sensitive logic on the client side.**
Any JavaScript code delivered to a browser can be read, modified, and re-executed by the user.
This means :

- Do not validate payments, permissions, or roles in the front-end layer alone.
- Do not store API secrets, passwords, or tokens inside your microMVC+ models or controllers.
- Do not trust data coming from the browser on your server. Always re-validate on the server.

**Never use microMVC+ state as the single source of truth for security decisions.**
A user can open the browser DevTools and modify any in-memory value at any time.

### What this means in practice

If you build a bank transfer feature using this framework (like the demo) :

- The amount deducted must be calculated and validated on the server.
- The new balance shown to the user must come from the server response, not from local arithmetic.
- The role of microMVC+ is only to display the result that the server returned. Nothing more.

### Recommended practices

- Always sanitize any data before injecting it into the DOM to prevent XSS attacks.
  Use `textContent` instead of `innerHTML` when inserting user-provided content.
- Never pass raw HTML strings from untrusted sources into view callbacks.
- Use HTTPS. Always.
- Use Content Security Policy headers on the server that serves your application.
- Keep dependencies minimal. microMVC+ has zero runtime dependencies by design.
  Be cautious when adding third-party libraries to a project using this framework.

### Reporting a vulnerability

If you discover a security issue in this library, please report it privately before disclosing it publicly.

Contact : open a private GitHub Security Advisory on the repository.

Please include :
- A description of the vulnerability.
- Steps to reproduce.
- The version of microMVC+ affected.
- Any suggested fix if you have one.

You will receive a response within 7 business days.
We do not run a bug bounty program at this time, but all valid reports are credited in the changelog.

### A note for beginners

If you are learning with this framework, understanding the client/server boundary is one of the
most important things you can take away.

The browser is a public environment. Everything inside it is readable by the person using it.
Your server is where trust lives. Your browser is where experience lives.
microMVC+ helps you build great experiences. Your server protects the data behind them.
