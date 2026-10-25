# Apartment Rental Management System

**SWEN 383 – Design Principles & Patterns**  
**Team: The Design Architects**

## Project overview

We are designing a small browser application that helps guests find and reserve apartments for short stays. Managers can maintain their apartment listings and view reservations for those listings.

The project applies the design principles and patterns covered in our course. Part 1 focuses on the project scope, UML diagrams, design risks and proposed solutions. The features below describe the planned application, not a completed implementation.

## Users and planned features

### Guests
- Search for apartments by city, check-in date, checkout date and number of guests.
- View available apartments and their nightly prices.
- Reserve an available apartment.
- View their own reservations.
- Cancel their own confirmed reservations before check-in.

### Managers
- Create and edit their own apartment listings.
- Deactivate listings so they cannot receive new reservations.
- View reservations for their apartments.

## Scope

The first version is a **single-browser, single-active-tab prototype**, using HTML, CSS and JavaScript classes. Seeded demo users provide guest and manager roles.

Local storage will preserve data after a page reload. An alternative in-memory storage mode will support repeatable checks during development; its data will not survive a reload.

The following are outside the initial scope:
- Online payments and refunds.
- Real registration, login and production authentication.
- Reviews, messaging, maps and external booking-platform integrations.
- Dynamic pricing.
- A shared server and simultaneous bookings across devices or tabs.

## Main booking rules

- Check-in cannot be in the past, and checkout must be after check-in.
- Checkout is excluded from a stay: a booking for 10–12 November does not overlap one for 12–14 November.
- The guest count must be positive and within the apartment's capacity.
- Only confirmed reservations block availability.
- The service checks availability again when a reservation is requested.
- The total price is calculated from the nightly rate and number of calendar nights, then stored with the reservation.
- Only the guest who owns a confirmed reservation can cancel it, and only before check-in.
- Cancellation changes the status to cancelled and preserves booking history.
- New or changed reservation data is saved before the application accepts the change and displays success. If saving fails, the previous accepted state remains unchanged.
- Deactivating a listing prevents new reservations without deleting existing booking history.

## Design structure

| Component | Responsibility |
|---|---|
| `RentalController` | Receives user actions, delegates business work and requests screen updates. |
| `RentalOperations` | Defines the operations provided by the service. |
| `RentalService` | Coordinates validation, availability checks, reservations and persistence. |
| `RentalRenderer` | Displays results and errors on the page. |
| `StorageHandler` | Defines the contract for loading and saving application data. |
| `User` | Holds user identity and role. |
| `Apartment` | Holds listing details, checks capacity and calculates a price. |
| `Reservation` | Holds booking details and applies status and cancellation rules. |
| `DateRange` | Represents stay dates and calculates nights and overlap. |

This separation supports **Single Responsibility** and **Dependency Inversion**. GRASP **Controller**, **Creator** and **Information Expert** guide where responsibilities belong.

## Design risks and planned patterns

| Risk | Proposed solution | Trade-off |
|---|---|---|
| Choosing storage could spread storage-specific decisions into booking rules. | **Factory Method:** concrete factories create local-storage or in-memory handlers and supply them to the service. | Additional factory and storage classes. |
| Diagnostic logging could clutter the service's business rules. | **Decorator:** an optional logging wrapper implements `RentalOperations` and forwards calls to the wrapped service. | Forwarding methods must stay aligned with the interface and preserve outcomes. |
| Conflicting reservations or false confirmation after a failed save. | Recheck availability, prepare an independent candidate state, save it and accept it only after success. | This addresses the single-tab prototype; shared multi-device booking would require an atomic server-side check and save. |

Factory Method and Decorator are planned extensions described in the proposal. The class diagram focuses on the core application and domain model.

## Team and UML responsibilities

| Member | Assigned responsibility | Diagram filename |
|---|---|---|
| Dren Hoda | Combined class diagram and architecture | `Dren_Hoda_Class_Diagram.puml` |
| Ar Bilalli | Project scope and apartment search sequence | `Ar_Bilalli_Sequence_Search.puml` |
| Jon Ymeri | Reservation sequence and storage risk | `Jon_Ymeri_Sequence_Reserve.puml` |
| Gent Shala | Cancellation sequence and logging risk | `Gent_Shala_Sequence_Cancel.puml` |

These are responsibility assignments. Each member's actual contributions are recorded through their own commits and reviews.

## Project documents

The Part 1 package uses the following organisation:

- `docs/`: one combined class diagram and three sequence diagrams, with editable PlantUML (`.puml`) files and matching PNG previews.
- `submission/SWEN383_P1_TheDesignArchitects.pdf`: the combined Part 1 proposal.
- `team/`: presentation and individual preparation materials, when included in the repository.

Open PNG files to view the diagrams, or use a PlantUML renderer to preview and edit the `.puml` sources. The combined class diagram replaces the earlier separate structure and domain views.

## Collaboration

Each member reviews and develops their assigned work using their own GitHub account. Our recommended workflow is to create a branch, commit a meaningful change, open a pull request, receive teammate feedback and merge into `main`.

If a diagram changes, update its PNG preview and the corresponding diagram in the proposal so the documents remain consistent.
