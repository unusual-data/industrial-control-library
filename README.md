# Industrial Control Library

> **Author:** Ibrahim Capar  
> **Domain:** Industrial Software Engineering & Automation  
> **License:** MIT License

## 1. Context & Objective
This repository acts as a **technical reference for Industrial Software Engineering**, focusing on vendor-agnostic control architectures and reusable logic.

The primary objective is to bridge **IT/OT convergence** gaps by applying **Object-Oriented Programming (OOP)** principles to the IEC 61131-3 standard. It serves as a centralized platform for developing, testing, and documenting scalable automation patterns.

## 2. Technical Stack
* **Core Language:** IEC 61131-3 Structured Text (ST).
* **Methodology:** OOP (Interfaces, Methods, Inheritance, Polymorphism), Design Patterns, Unit Testing , SOLID Principles.
* **Target Platforms:** Beckhoff TwinCAT 3, CODESYS V3.
* **Tools:** Git (Version Control).

## 3. Repository Structure
The project follows a modular architecture to ensure separation of concerns:

```text
industrial-control-library/
├── docs/               # Technical documentation and coding standards
├── src/
│   ├── definitions/    # Shared Interfaces (I_*), DUTs, and Global Constants
│   ├── components/     # Hardware Abstraction Layer
│   ├── patterns/       # Software Design Patterns (State, Observer, Command)
│   ├── gateways/       # IT/OT connectivity 
│   ├── distributed/    # Event-driven blocks (IEC 61499 concepts)
│   └── targets/        # Platform-specific project files (TwinCAT, TIA, etc.)
├── tests/              # Unit and Integration tests
├── tools/              
└── examples/            
```
