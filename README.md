Project Overview

The Apex Trigger Actions Framework is a scalable and metadata-driven framework designed to simplify and standardize record-triggered automation on the Salesforce Platform.

It enables developers and administrators to decouple trigger logic, control execution order, and combine Apex and Flow automations in a single, unified model—without continuously modifying trigger code.

This framework introduces an Automation Studio–style view of all automations related to a specific sObject, making complex automation ecosystems easier to manage, audit, and evolve.

Business Value
For Developers

Clean, modular trigger architecture

Strong adherence to SOLID principles

Reduced technical debt and easier extensibility

For Admins

Configuration-driven automation using Custom Metadata

Clear visibility into automation order and behavior

Flow-first or Apex-first flexibility

For Enterprises

Predictable execution order

Safe bypass mechanisms for integrations and data loads

Enterprise-ready governance and scalability

Core Principles

The framework is built around proven software design principles:

Single Responsibility Principle
Each automation action handles one specific responsibility.

Open–Closed Principle
New automations can be added without modifying existing trigger logic.

Strategy Pattern
Automation actions are dynamically selected and executed at runtime.

Architecture Overview
┌─────────────────────────────────────────────────────────┐
│                    SALESFORCE PLATFORM                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌───────────────────────────────────────────────────┐ │
│  │               STANDARD APEX TRIGGERS               │ │
│  │         (Thin Triggers – Single Entry Point)       │ │
│  └───────────────────────────────────────────────────┘ │
│                         │                               │
│  ┌──────────────────────┴──────────────────────────┐  │
│  │           METADATA TRIGGER HANDLER                │  │
│  │  - Reads Custom Metadata                          │  │
│  │  - Determines order & context                     │  │
│  │  - Executes actions dynamically                   │  │
│  └──────────────────────┬──────────────────────────┘  │
│                         │                               │
│  ┌──────────────────────┴──────────────────────────┐  │
│  │        APEX & FLOW TRIGGER ACTIONS                │  │
│  │  - Apex Classes                                  │  │
│  │  - Auto-Launched Flows                            │  │
│  │  - Conditional Execution                          │  │
│  └──────────────────────┬──────────────────────────┘  │
│                         │                               │
│  ┌──────────────────────┴──────────────────────────┐  │
│  │              DML FINALIZERS (Optional)            │  │
│  │     Executes once at the end of a transaction     │  │
│  └──────────────────────────────────────────────────┘  │
│                                                         │
└─────────────────────────────────────────────────────────┘

Metadata-Driven Automation Model

All automation behavior is configured using Custom Metadata Types, eliminating hard-coded logic in triggers.

Core Metadata Objects

SObject_Trigger_Setting__mdt

Defines enabled objects

Controls trigger contexts

Specifies TriggerRecord classes

Trigger_Action__mdt

Defines Apex or Flow actions

Controls execution order

Supports bypass and permissions

DML_Finalizer__mdt

Registers finalizers

Controls post-transaction execution

Supported Automation Types

Apex Trigger Actions

Auto-Launched Flow Trigger Actions

Change Data Capture (CDC) Flow Actions

Transaction-Level DML Finalizers

Application Flow

Record enters trigger context (insert/update/delete)

MetadataTriggerHandler executes

Metadata for the object and context is evaluated

Apex and Flow actions are executed in defined order

Entry criteria and bypass rules are applied

Optional DML finalizers execute once at transaction end

Key Capabilities

Metadata-based execution sequencing

Apex and Flow interoperability

Per-action entry criteria formulas

Global and transactional bypass controls

Recursion prevention utilities

DML-less unit testing support

Enterprise-grade extensibility

Apex Trigger Actions

Each automation is implemented as a small, focused Apex class that implements one or more TriggerAction interfaces.

Supported interfaces include:

BeforeInsert

AfterInsert

BeforeUpdate

AfterUpdate

BeforeDelete

AfterDelete

AfterUndelete

These actions are dynamically instantiated using reflection at runtime.

⚠️ If an Apex class is referenced in metadata but does not exist or implement the correct interface, a runtime error will occur.

Flow Trigger Actions

The framework supports auto-launched Flows as first-class trigger actions.

Flow Requirements

Flows must define:

record (input/output)

recordPrior (input only, where applicable)

Flows are invoked via:

TriggerActionFlow

TriggerActionFlowChangeEvent (CDC)

⚠️ Flow recursion is limited to a maximum depth of 3 due to platform constraints.

Entry Criteria & Conditional Execution

Each trigger action can define its own entry criteria formula, evaluated dynamically at runtime.

Formula is evaluated per record

Supports record and recordPrior

If empty → action executes for all records

⚠️ Cross-object field traversal is not supported.

Recursion Control & Bypass Mechanisms
Global Bypass

Bypass entire objects or actions via metadata checkboxes

Transaction-Level Bypass

Bypass objects, Apex actions, or Flows programmatically

Supported in both Apex and Flow

User-Based Control

Required permissions

Bypass permissions for integrations or admins

DML Finalizers

DML Finalizers execute exactly once at the end of a DML transaction.

Use Cases

Enqueue async jobs

Aggregate logs

Post-commit processing

Key Characteristics

No DML allowed inside finalizers

Independent of triggering sObject

Metadata-driven execution order

⚠️ Finalizers are experimental and should be used cautiously.

Testing Strategy (DML-less Testing)

The framework enables zero-DML unit testing by:

Generating fake record IDs

Testing addError() logic directly

Avoiding expensive DML operations

This results in:

Faster test execution

Lower CPU usage

Higher test reliability

Best Practices

Keep trigger actions small and focused

Use entry criteria formulas aggressively

Centralize queries using Singleton patterns

Guard against recursive execution

Always configure bypass options for integrations

Setup & Configuration
Step 1: Enable Trigger

Add MetadataTriggerHandler().run() to the object trigger.

Step 2: Configure SObject Metadata

Create an SObject_Trigger_Setting__mdt record.

Step 3: Define Trigger Actions

Create Apex classes or Flows and register them in Trigger_Action__mdt.

Step 4: (Optional) Configure Finalizers

Register finalizers using DML_Finalizer__mdt.

Step 5: Test & Deploy

Validate order, bypass rules, and recursion behavior.

Documentation

This project supports ApexDocs for automatic documentation generation.

Documentation can be generated in Markdown format and served using static site tools.
