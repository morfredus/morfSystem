# morfSystem

> **An ecosystem of autonomous, free and interoperable services, designed to run first at its user's home, not at its vendor's.**

[![Version](https://img.shields.io/badge/version-1.4.13-blue)](CHANGELOG.md)
[![Doc](https://img.shields.io/badge/type-documentation-informational)](docs/)
[![License](https://img.shields.io/badge/License-GPL--3.0--only-blue)](LICENSE)

*Read in another language: **English** (this document) · [Français](README.fr.md).*

> The reference documentation itself lives in `docs/` and is currently written in French.

---

# Documentation

This repository is the reference documentation of the morfSystem ecosystem.

If you are discovering the project, we recommend the following reading order:

## Discover

- 📖 [Getting started](docs/GETTING-STARTED.md)
- 🚀 [Install & build](https://github.com/morfredus/morfTools/blob/main/docs/GUIDE-DEMARRAGE.md) — the hands-on guide: clone, compile, install the services
- 🔄 [Test a local update](docs/TESTER-MISE-A-JOUR-LOCALE.md) — a step-by-step French guide to configure morfUpdate and the morfMonitor button
- 📖 [Philosophy](docs/PHILOSOPHY.md) — *why* the ecosystem is designed this way
- 📖 [Architecture](docs/ARCHITECTURE.md) — *how* the pieces fit together
- 📖 [Thinking in morfSystem](docs/THINKING-IN-MORFSYSTEM.md)

## Understand

- 📖 [Architecture principles](docs/ARCHITECTURE-PRINCIPLES.md)
- 📖 [Contracts](docs/CONTRACTS.md)
- 📖 [Design decisions](docs/DESIGN-DECISIONS.md)
- 📖 [Decision framework](docs/DECISION-FRAMEWORK.md)
- 📖 [Development rules](docs/DEVELOPMENT-RULES.md)
- 📖 [Local data layout](docs/FILESYSTEM.md)
- 📖 [Governance](docs/GOVERNANCE.md)
- 📖 [Project lifecycle](docs/PROJECT-LIFECYCLE.md)

## Contribute

- 📖 [Contributing](docs/CONTRIBUTING.md) — how to take part, and the hard rules
- 📖 [New-project checklist](docs/checklists/NEW-PROJECT.md)

## Reference

- 📖 [Ecosystem](docs/ECOSYSTEM.md)
- 📖 [Glossary](docs/GLOSSARY.md) — the key terms at a glance
- 📖 [Roadmap](docs/ROADMAP.md)
- 📖 [Real-world validation](docs/VALIDATION-REELLE-MORFSYSTEM.md) — what was actually compiled, installed and used on the fleet (French)
- 📖 [What morfSystem is not](docs/NON-GOALS.md)
- 📖 [FAQ](docs/FAQ.md)

---

## Why morfSystem?

morfSystem was not born from the wish to create a new platform, a new framework or a new software suite.

It was born from an observation.

Over the years, many personal projects appeared: supervision tools, connected sensors, desktop applications, network services, system utilities...

Each project answered a precise need.

Each project worked.

But each one reinvented part of the same mechanisms: network discovery, supervision, configuration, updates, communication between applications, deployment...

The simplest solution would have been to merge everything into a single application.

That choice was deliberately rejected.

Conversely, the current trend often consists of splitting applications into a multitude of tightly coupled microservices, to the point that none can really run on its own.

That choice was rejected too.

morfSystem was born between these two extremes.

The goal is not to build a platform.

The goal is to build a coherent ecosystem.

Each component has a clearly defined responsibility.

Each component remains usable on its own.

When they are present together on the same network, they collaborate naturally through shared contracts rather than through imposed dependencies.

This difference is fundamental.

morfSystem never forces one component upon you because another is installed.

It offers cooperation, never an obligation.

> To understand the motivations behind this architecture, read the **[Philosophy](docs/PHILOSOPHY.md)**.

---

# What morfSystem is

morfSystem is a set of free software programs sharing the same design philosophy.

It is not a monolithic software suite.

It is not an operating system.

It is not a framework.

Each project has its own repository, its own development cycle, its own documentation and its own responsibility.

Components share only what genuinely needs to be shared:

- conventions;
- exchange contracts;
- documented protocols;
- a common philosophy.

Everything else belongs to each project.

This approach lets each component evolve without calling the whole ecosystem into question.

> The overall organisation is presented in **[Architecture](docs/ARCHITECTURE.md)**.

---

# What morfSystem is not

Understanding what morfSystem refuses is as important as understanding what it offers.

morfSystem is not a framework: it imposes no way of developing, only principles.

It is not a software suite: you install only the components you need.

It is not an operating system: it respects the differences between platforms rather than hiding them.

It is not *cloud first*: the nominal operation is local, remote access remains an extension.

It is not an orchestrator: no central component drives the others.

> This vision is developed in **[What morfSystem is not](docs/NON-GOALS.md)**.

---

# The founding principles

## One responsibility per service

Each project exists for a single reason, expressible in one sentence. A new need yields a new project, never a more complicated one.

## Autonomy first

A component must be useful even on its own. Cooperation with others is an improvement, never a condition for it to work.

## The local network is the truth

Services are designed to run on their user's network, with no remote infrastructure. External access is a possible extension, never an obligation.

## Contracts over knowledge

Components collaborate through their public interfaces, never through their internal workings. An implementation can be rewritten; the contract stays stable.

## Standards over magic

When an open standard answers the need (HTTP, JSON, UDP, text files), it is preferred. Reaching for a specific technology must always be justified.

## Simplicity over hype

We solve the problem with the minimum necessary complexity. A proven solution is often better than a newer but more complex one.

> The principles are detailed in **[Architecture principles](docs/ARCHITECTURE-PRINCIPLES.md)**.

---

# Shared governance

Although the components stay independent, they share a set of conventions.

A clearly identified responsibility, a public API when a service communicates, network discovery through morfBeacon, usable logs, an explicit version number, a free licence: these conventions change little and form the stability of the ecosystem.

The common tools, such as morfTools or morfBeacon, help this coherence without becoming mandatory dependencies.

> The shared conventions are described in **[Governance](docs/GOVERNANCE.md)** and **[Development rules](docs/DEVELOPMENT-RULES.md)**.

---

# A philosophy before a technology

morfSystem does not try to follow trends.

It tries to build a coherent, robust and understandable whole.

Languages, libraries and platforms are only means.

A component is judged by its responsibility, never by its technical stack.

That is why a technology is never adopted simply because it is recent.

Before adding a feature, it seeks to preserve simplicity.

It is this philosophy that ties all the projects together.

Far more than their code.

---

# Going further

The **morfSystem** repository describes the common principles of the ecosystem.

Each project then has its own dedicated documentation.

If you want to dig deeper, we recommend the following reading order:

1. **[Getting started](docs/GETTING-STARTED.md)**
2. **[Philosophy](docs/PHILOSOPHY.md)**
3. **[Architecture](docs/ARCHITECTURE.md)**
4. **[Architecture principles](docs/ARCHITECTURE-PRINCIPLES.md)**
5. **[Development rules](docs/DEVELOPMENT-RULES.md)**
6. **[Governance](docs/GOVERNANCE.md)**
7. **[Ecosystem](docs/ECOSYSTEM.md)**

Want to **take part**? Read **[Contributing](docs/CONTRIBUTING.md)**: how to propose a component, the conventions to follow and the hard rules.
