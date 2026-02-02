# HaikUI: A SwiftUI‑Inspired DSL for Haiku OS

*Designing a declarative UI system in YAB, inspired by SwiftUI but grounded in Haiku’s native widgets.*

---

## Overview

HaikUI is a small declarative UI DSL for **Haiku OS**, implemented entirely in **YAB**.  
It borrows the *mental model* of **SwiftUI**—nested views, readable structure, and modifiers—while remaining pragmatic and faithful to Haiku’s native widget system.

Rather than attempting to replicate SwiftUI’s full reactive and diffing engine, HaikUI focuses on a clear, minimal pipeline:

**DSL → Node Registry → Renderer → Native Haiku Widgets**

This approach keeps the system simple, debuggable, and well‑suited to the constraints of YAB.

---

## Declarative Structure

HaikUI describes user interfaces as a tree of containers and controls.

Containers such as `Window`, `HStack`, `VStack`, `Boxview`, and `Tabview` define structure, while widgets like `Button`, `Label`, and `Slider` provide interaction.

The emphasis is on **composition**, not boilerplate:
- Windows contain layouts  
- Layouts contain containers  
- Containers contain controls  

---

## Example: HaikUI DSL

Below is a real HaikUI layout used in a simple drawing application.  
It defines a window split into a controls panel (left) and a canvas area (right).

```
Window(title="Draw Demo", x="10", y="10", w="560", h="460") {
  HStack(x="5", y="5", w="540", h="450") {

    Boxview(title="Controls", x="10", y="10", w="260", h="440", lineType="2") {
      VStack(x="5", y="5", w="240", h="410", pad="0", space="10") {

        HStack(w="240", h="200", pad="0", space="10") {

          VStack(w="100", h="200", pad="0", space="10") {
            Button(title="New object", w="100", h="30")
            Button(title="Clear", w="100", h="30")

            Radiobutton(title="Line", w="100", h="24", isActivated="1")
            Radiobutton(title="Rect", w="100", h="24")
            Radiobutton(title="Round rect", w="100", h="24")
            Radiobutton(title="Ellipse", w="100", h="24")
          }

          VStack(w="130", h="200", pad="0", space="5") {
            Label(title="Object", w="190", h="18",
                  font="Noto Sans Display", style="Bold", size="12")
            Listbox(w="130", h="160", scrollbarType="1")
          }
        }

        Colorcontrol(w="230", h="60")
        Textcontrol(title="100", w="70", h="22")
        Checkbox(title="Fill", w="70", h="22")
        Slider(title="Width", w="130", h="25", min="1", max="100",
               option="Block-horizontal")
      }
    }

    VStack(x="270", y="20", w="280", h="440", background="#FFFFFF") {
      Label(title="Click and drag to draw an object",
            x="50", y="210", w="220", h="20",
            font="Noto Sans Display", style="Bold", size="12")
    }

  }
}
```

The structure of the code directly mirrors the visual hierarchy of the interface.

---

## The Registry: HaikUI’s Intermediate Representation

After parsing, HaikUI converts the DSL into a **registry**: a flat list of node records.

Each record includes:
- A unique ID
- Widget type
- Geometry (x, y, width, height)
- Parent ID
- Optional properties (modifiers)

Example registry fields:

```
ID | Type | Label | X | Y | Width | Height | Parent | Properties...
```

This registry acts as HaikUI’s internal “view model” and makes rendering and debugging straightforward.

---

## Parenting and the Container Stack

Correct parent–child relationships are the core challenge of any declarative UI system.

HaikUI enforces correctness using a **container stack**:
- Entering a container pushes it onto the stack
- Exiting a container pops it
- New nodes attach to the current top of the stack

This guarantees correct hierarchy even with deeply nested layouts.

---

## Layout Model: SwiftUI vs HaikUI

**SwiftUI**
- Dynamic measurement and placement
- Parent/child negotiation
- Runtime reconciliation

**HaikUI**
- Deterministic flow layout
- Cursor‑based positioning in stacks
- Explicit padding and spacing
- Optional absolute overrides

The result is predictable, debuggable layout behavior.

---

## Modifiers as Data

In SwiftUI, modifiers are compositional API calls.

In HaikUI, modifiers are parsed as **data**:
- Stored as key–value properties
- Interpreted by the renderer

This data‑driven design keeps the renderer small and well‑suited to YAB.

---

## SwiftUI vs HaikUI at a Glance

| Aspect | SwiftUI | HaikUI |
|------|---------|--------|
| Authoring style | Declarative API | Declarative DSL |
| View model | Value‑type tree | Registry records |
| Layout | Measurement‑based | Flow‑based |
| Modifiers | API transforms | Data properties |
| State | Reactive | Explicit |
| Widgets | Framework views | Native Haiku widgets |
| Debugging | Runtime tools | Registry dump |
| Implementation | Large framework | Lightweight renderer |

---

## Conclusion

HaikUI shows that modern declarative UI concepts can be adapted to constrained environments without sacrificing clarity or control.

By combining a SwiftUI‑like authoring style with a simple registry and deterministic renderer, HaikUI offers a practical, Haiku‑native way to build user interfaces—focused on correctness, readability, and ease of iteration.
