# CSG 3023 - Midterm Exam Study Guide

## Module 1: Version Control Systems & Git Workflow

### Git vs. GitHub vs. GitHub Desktop

| Tool               | Purpose                                                                                                                             |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| **Git**            | A distributed version control system (VCS) that runs locally and tracks file changes and commit history.                            |
| **GitHub**         | A cloud-based service that hosts remote Git repositories and supports collaboration, code review, Pull Requests, and remote backup. |
| **GitHub Desktop** | A graphical user interface (GUI) that allows developers to perform Git operations without using the command line.                   |

### Git LFS — Large File Storage

Standard Git is optimized for relatively small, text-based files such as source code. Game projects frequently contain large binary assets, including:

* FBX 3D models
* PNG/TGA textures
* WAV/MP3 audio
* Other large binary assets

**Git LFS** replaces large binary files in the main Git repository with lightweight text pointers. The actual binary files are stored separately in LFS storage.

Large binary file extensions are configured in the:

```text
.gitattributes
```

---

### 🌿 Branching Taxonomy

A **branch** represents an independent line of development. Branches isolate unverified work from the stable `main` codebase.

| Branch Type  | Purpose                                                         | Example                      |
| ------------ | --------------------------------------------------------------- | ---------------------------- |
| **Main**     | Stable, production-ready release state.                         | `main`                       |
| **Feature**  | Development of a new system or feature.                         | `feat/player-controller`     |
| **Fix**      | Isolated correction of a bug.                                   | `fix/puzzle-reset`           |
| **Refactor** | Restructures code or assets without changing external behavior. | `refactor/project-structure` |
| **Test**     | Experimental or sandbox development.                            | `test/new-input-system`      |
| **Release**  | Milestone preparation and release-candidate polishing.          | `release/prototype-rapid`    |

---

## 🔄 Git Lifecycle Stages
`Working Directory → Staging Area → Local Repository → Remote Repository → Pull Request → Merge`
| Stage                    | What It Means                                                                                                                                   | Common Command                              |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| **1. Working Directory** | The project files currently stored on the local computer, including uncommitted changes.                                                        | —                                           |
| **2. Staging Area**      | The group of modifications selected to be included in the next commit.                                                                          | `git add .`                                 |
| **3. Local Repository**  | A recorded snapshot of the staged changes stored in the local repository.                                                                       | `git commit -m "feat: add player movement"` |
| **4. Remote Repository** | Uploads local commits to the corresponding branch on the remote repository, such as GitHub.                                                     | `git push origin <branch-name>`             |
| **5. Pull Request**      | A GitHub feature used to review, discuss, approve, and merge changes from one branch into another, typically from a feature branch into `main`. | —                                           |


---

## 🔁 Development Session Workflow

### Session Start

Before beginning work, update the local `main` branch and merge the latest changes into the working branch:

```bash
git checkout main
git pull origin main
git checkout feat/player-controller
git merge main
```

### Commit Cadence

Commit at **logical, stable working points**.

Good commit points include:

* A movement system works
* A jump mechanic is complete
* A UI system is functional
* A bug has been fixed
* A meaningful refactor has been completed

A reasonable guideline during active development is approximately **one commit per hour**.

---

### 📝 Standardized Commit Messages

Use the format: `type: description`

| Type        | Purpose                                               | Example                                   |
| ----------- | ----------------------------------------------------- | ----------------------------------------- |
| `feat:`     | Adds a new feature or capability.                     | `feat: add player movement`               |
| `fix:`      | Corrects a bug or unintended behavior.                | `fix: correct jump input`                 |
| `refactor:` | Restructures code without changing external behavior. | `refactor: simplify movement calculation` |
| `docs:`     | Updates documentation.                                | `docs: update setup instructions`         |
| `test:`     | Adds or modifies tests.                               | `test: add movement tests`                |
| `chore:`    | General maintenance or configuration changes.         | `chore: update Unity settings`            |

---

# 🧪 Scenario-Based Practice

## Question 1

A developer imports 4K textures and high-poly 3D character models totaling 1.5 GB. When attempting to push the feature branch to GitHub, the push fails because of file-size restrictions.

What is the correct technical remedy?

- **A.** Merge the feature branch into `main` locally, then push directly to GitHub.
- **B.** Delete the local `.git` folder and reinitialize the repository using `git init`.
- **C.** Install Git LFS, track the binary extensions, and register them in `.gitattributes`.
- **D.** Convert private fields to public variables so Git can compress them.

**Correct Answer: C**

**Why:** Git LFS is designed to manage large binary assets such as FBX files, textures, and audio. It stores lightweight pointers in the Git repository while storing the binary payload separately.

---

## Question 2

A programmer begins work on `feat/moving-platform`. Before writing new code, what sequence ensures the branch contains the latest changes from `main`?

- **A.** `git checkout main` → `git pull origin main` → `git checkout feat/moving-platform` → `git merge main`
- **B.** `git push origin main` → `git checkout feat/moving-platform`
- **C.** `git commit -m "chore: sync"` → `git push origin feat/moving-platform`
- **D.** `git checkout main` → `git add .` → `git commit -m "fix: merge"`

**Correct Answer: A**

**Why:** The local `main` branch must first be updated from the remote repository. Those changes are then merged into the active development branch.

---

# Module 2: C# Fundametals

### Encapsulation & Access Modifiers

**Encapsulation** protects internal class data by controlling how other classes access and modify that data.

| Modifier    | Access                             |
| ----------- | ---------------------------------- |
| `private`   | Only the defining class            |
| `protected` | Defining class and derived classes |
| `public`    | Any external class                 |

### Fields vs. Properties

Fields store internal data and **should generally remain `private`**.
Properties provide **controlled access** and **validate** by excuting logic when a value is assigned.

```csharp
private float _speed;
private Vector3 _direction;

public float Speed
{
    get => _speed;
    set => _speed = Mathf.Clamp(value, 0f, MAX_SPEED);
}

public Vector3 Direction
{
    get => _direction;
    set => _direction = value.normalized;
}
```

### ⚠️ Unity Inspector Boundary

Unity's Inspector assigns values directly to serialized fields. It does **not** automatically invoke the property's setter.

Therefore, validation can be enforced during initialization:

```csharp
private void Awake()
{
    Speed = _speed;
    Direction = _direction;
}
```

---

### 🎛️ Inspector Attributes

| Attribute           | Purpose                                                   |
| ------------------- | --------------------------------------------------------- |
| `[SerializeField]`  | Exposes a private field to the Inspector.                 |
| `[Tooltip("...")]`  | Adds documentation when hovering over an Inspector field. |
| `[Header("...")]`   | Creates a visual grouping label in the Inspector.         |
| `[Range(min, max)]` | Displays a numeric field as a slider.                     |

---

### 🧩 Core Software Design Principles
| Principle                                 | Meaning                                                                | Example / Guidance                                                                                                                                                                          |
| ----------------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SRP — Single Responsibility Principle** | A class should have one primary responsibility.                        | **Movement component** → movement<br>**Health component** → health<br>**Audio component** → audio                                                                                           |
| **DRY — Don't Repeat Yourself**           | Avoid unnecessary duplication by centralizing shared logic.            | **Architectural Trade-Off:** Small amounts of duplication can sometimes be preferable when independence, readability, and self-containment are more important than creating an abstraction. |
| **KISS — Keep It Simple**                 | Prefer simple, readable solutions over unnecessary complexity.         | Avoid adding abstractions, patterns, or systems that are not needed to solve the current problem.                                                                                           |
| **YAGNI — You Aren't Gonna Need It**      | Implement what is currently required rather than speculative features. | Don't build features or infrastructure based only on what *might* be needed later.                                                                                                          |

---

### 🛠️ Coding & Documentation Standards


| Area                       | Expectations                                                                                                                            |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Code Standards**         | Adhere to the course C# coding standards.                                                                                               |
| **Code Structure**         | Each class and method should follow the Single Responsibility Principle and have one clear purpose.                                     |
| **Magic Numbers**          | Avoid magic numbers. Define numeric values as named variables, properties, or constants when they represent a value or calculation.     |
| **Script Template**        | Use the CSG script template for all new scripts.                                                                                        |
| **Documentation Comments** | Every class must include a documentation comment describing its purpose. All fields and properties should be documented as appropriate. |
| **Method Documentation**   | Every public method must include an XML documentation comment explaining its purpose and use.                                           |
| **Inline Comments**        | Add comments to clarify non-obvious logic.                                                                                              |
| **Naming Conventions**     | Follow the course C# naming conventions and asset naming guidelines.                                                                    |
| **Component Design**       | Modular components should provide reusable functionality with a clear responsibility.                                                   |
| **Behavioral Design**      | Behavioral classes should define game-specific functionality and coordinate components when appropriate.                                |

---

### 🏷️ Naming Conventions

| Element         | Convention          | Example                           |
| --------------- | ------------------- | --------------------------------- |
| Classes         | `PascalCase`        | `MoveTransform`                   |
| Properties      | `PascalCase`        | `Speed`                           |
| Methods         | `PascalCase`        | `CalculateDisplacement()`         |
| Enums           | `PascalCase`        | `MovementState`                   |
| Private fields  | `_camelCase`        | `_speed`                          |
| Local variables | `camelCase`         | `elapsedTime`                     |
| Parameters      | `camelCase`         | `speedValue`                      |
| Constants       | `UPPER_SNAKE_CASE`  | `MAX_SPEED`                       |
| Static fields   | `s_PascalCase`      | `s_InstanceCount`                 |
| Boolean values  | Question/state form | `_isMoving`, `_hasKey`, `CanJump` |

---
### 📁 Namespace Taxonomy

Namespaces organize code and should **mirror the folder structure**.

```text
Assets/Scripts/CSG/Physics/MoveRigidbodyPosition.cs
        ↓
namespace CSG.Physics
{
    public class MoveRigidbodyPosition : MonoBehaviour
    {
        // ...
    }
}
```

**General structure:** `Company.Project.Category`

---

### 🔄 Unity Lifecycle

Important lifecycle callbacks include:

| Callback        | Purpose                                                            |
| --------------- | ------------------------------------------------------------------ |
| `Awake()`       | One-time initialization when the object is loaded.                 |
| `OnEnable()`    | Runs when the component/GameObject becomes active.                 |
| `Start()`       | One-time initialization before the first frame update, if enabled. |
| `FixedUpdate()` | Fixed-timestep update used for physics calculations.               |
| `Update()`      | Per-frame update used for input, timers, and non-physics behavior. |
| `OnDisable()`   | Runs when the component/GameObject becomes inactive.               |
| `OnDestroy()`   | Runs when the object is destroyed.                                 |

---

# 🧪 Scenario-Based Practice

## Question 3

A level designer enters `999f` for the `Speed` field. The script defines:

```csharp
private const float MAX_SPEED = 20f;
```

and a property setter using `Mathf.Clamp()`. However, the object still moves at 999 units per second.

What caused the failure?

- **A.** `[SerializeField]` permanently overrides property setters.
- **B.** Unity's Inspector assigned `999f` directly to `_speed`, bypassing the property setter because `Awake()` did not execute `Speed = _speed;`.
- **C.** The property uses `PascalCase`.
- **D.** Floating-point values cannot be clamped in a `MonoBehaviour`.

**Correct Answer: B**

**Why:** Unity serializes the field directly. The property setter is not automatically invoked when the Inspector assigns the value.

---

## Question 4

Review the following code:

```csharp
public class PlayerHealth : MonoBehaviour
{
    public int health = 100;

    public void TakeDamage(int amount)
    {
        health -= amount;

        if (health <= 0)
        {
            Destroy(gameObject);
        }
    }
}
```

Which standard is violated?
- **A.** SRP, because the class derives from `MonoBehaviour`.
- **B.** Static methods are used incorrectly.
- **C.** YAGNI, because `TakeDamage()` accepts an integer parameter.
- **D.** Encapsulation is violated by exposing `health` as a public field without controlled access or validation, and the naming convention is incorrect.

**Correct Answer: D**

**Why:** External classes can directly modify `health` without going through the logic that manages damage and death.

---

# Module 3: Spatial Transformations

### ⏱️ Frame Rate Independence

Movement should account for elapsed time so speed remains consistent across different frame rates.

```text
Displacement = Speed × Time.deltaTime × Direction
```

```csharp
transform.position += Speed * Time.deltaTime * Direction;
```

At 60 FPS, `Time.deltaTime ≈ 0.0166` seconds.

---

### 📐 Vector Magnitude & Normalization

#### Magnitude

The length of a vector:

```text
Magnitude = √(x² + y² + z²)
```

#### Normalization

`.normalized` creates a vector with a magnitude of **1** while preserving its direction.

```csharp
_direction = value.normalized;
```

> **Rule:** Direction vectors used to represent movement should generally be normalized when movement speed must remain consistent.

A direction vector with a magnitude of 5 would otherwise cause movement to occur **5× faster** than the same movement using a unit vector.

---

### ⚙️ Coordinate Spaces

| Space           | Description                                                 | Unity         |
| --------------- | ----------------------------------------------------------- | ------------- |
| **World Space** | Global scene coordinates and axes.                          | `Space.World` |
| **Local Space** | Coordinates relative to the object's orientation or parent. | `Space.Self`  |

---

### Common Transform Operations

| Operation               | Type     | Space / Behavior                                                                                |
| ----------------------- | -------- | ----------------------------------------------------------------------------------------------- |
| `transform.position`    | Property | Gets or sets the object's **world-space position**.                                             |
| `transform.Translate()` | Method   | **Moves** the object using a specified coordinate space, such as `Space.World` or `Space.Self`. |

---

## 🔄 Euler Angles vs. Quaternions

| Representation   | Values       | Advantages                                                          | Considerations                  |
| ---------------- | ------------ | ------------------------------------------------------------------- | ------------------------------- |
| **Euler Angles** | `X, Y, Z`    | Human-readable; used in the Unity Inspector                         | Can experience **Gimbal Lock**  |
| **Quaternions**  | `X, Y, Z, W` | Used internally by Unity; avoid the traditional gimbal-lock problem | Less intuitive to read directly |

Convert Euler angles to a quaternion with:

```csharp
Quaternion.Euler(x, y, z);
```

---

# 🧪 Scenario-Based Practice

## Question 5

A developer writes:

```csharp
void Update()
{
    transform.position += new Vector3(0, 0, 10);
}
```

The object moves much farther per second on a 240 FPS computer than on a 30 FPS device.

What accounts for this behavior?

- **A.** Gimbal Lock
- **B.** Frame-rate-dependent movement caused by applying displacement once per frame without `Time.deltaTime`
- **C.** A missing kinematic Rigidbody
- **D.** A missing `Awake()` method

**Correct Answer: B**

**Why:** Raw displacement is applied once for every rendered frame. Multiplying by `Time.deltaTime` converts the calculation into a time-based displacement.

---

## Question 6

A turret calculates:

```csharp
Vector3 targetDirection = target.position - transform.position;
```

What should happen before using this vector for constant-speed movement?
- **A.** Multiply by `Time.fixedDeltaTime`.
- **B.** Convert it to Euler angles.
- **C.** Normalize the vector.
- **D.** Pass it to `GetComponent<Rigidbody>()`.

**Correct Answer: C**

**Why:** The difference between two positions has a magnitude equal to the distance between them. Normalizing produces a unit direction vector.

---

## Module 4: Unity Physics

### ⚙️ Rigidbody Body Types

Yes. I’d fold the explanatory details into the **Common Behavior** column so the table stands on its own:

| Type          | Configuration         | Driven By             | Common Uses / Behavior                                                                                                            |
| ------------- | --------------------- | --------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Dynamic**   | `isKinematic = false` | PhysX simulation      | Player physics, debris, falling objects. Responds to **gravity, forces, collisions, and torque**.                                 |
| **Kinematic** | `isKinematic = true`  | Scripts / animation   | Moving platforms, elevators, doors. Controlled by scripts or animation rather than forces; can interact with Dynamic Rigidbodies. |
| **Static**    | No Rigidbody          | Static scene geometry | Floors, walls, terrain. A Collider without a Rigidbody is treated as static scene geometry.                                       |

---

### 🚚 Transform vs. Physics Movement

Physics force calculations belong in:

```csharp
FixedUpdate()
```

#### Transform Movement

| Method                                  | Used With               | Description                                                                        | Typical Usage                         |
| --------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------- | ------------------------------------- |
| `transform.position = newPosition;`     | Any `Transform`         | Directly changes the object's position and can bypass physics calculations.        | General Transform-based movement      |
| `_rigidBody.MovePosition(newPosition);` | **Kinematic Rigidbody** | Moves a Rigidbody through the physics system while maintaining Rigidbody behavior. | Typically called from `FixedUpdate()` |

> **Rule:** Avoid direct `Transform` movement on **Dynamic Rigidbodies**. Use Rigidbody-based movement instead.

---

### Rigidbody Velocity vs Force

| Method                     | Purpose                                    | Key Behavior                                                                          | Common Usage                            |
| -------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------- | --------------------------------------- |
| `Rigidbody.linearVelocity` | Directly sets physical velocity.           | Sets velocity in **units per second**; do **not** multiply by `Time.deltaTime`.       | Direct, controlled movement             |
| `Rigidbody.AddForce()`     | Applies a physical force to the Rigidbody. | Uses a selected `ForceMode`: `Force`, `Acceleration`, `Impulse`, or `VelocityChange`. | Physics-based acceleration and movement |

---

#### ⚡ Force Modes
The mode used to apply force to the Rigidbody

| Force Mode           | Applies      | Depends On Mass? | Depends On Time? | Description                                                                                                                      |
| -------------------- | ------------ | ---------------: | ---------------: | -------------------------------------------------------------------------------------------------------------------------------- |
| **`Force`**          | Force        |          **Yes** |          **Yes** | Applies a continuous force that produces acceleration based on the Rigidbody's mass and the amount of time the force is applied. |
| **`Acceleration`**   | Acceleration |           **No** |          **Yes** | Applies acceleration directly, independent of the Rigidbody's mass.                                                              |
| **`Impulse`**        | Sudden force |          **Yes** |           **No** | Applies a sudden change in momentum, such as a jump or explosion.                                                                |
| **`VelocityChange`** | Velocity     |           **No** |           **No** | Directly changes the Rigidbody's velocity, independent of mass and time.                                                         |

This makes the key distinction easy to remember: **Force and Impulse account for mass; Force and Acceleration account for time; VelocityChange accounts for neither.**


---

### 💥 Collision vs. Trigger

| Feature              | **Collision**                                                                                                | **Trigger**                                                                               |
| -------------------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| **Collider Setting** | `isTrigger = false`                                                                                          | `isTrigger = true`                                                                        |
| **Behavior**         | Creates a **physical barrier** and participates in physical collision response.                              | Detects **overlap** without creating a physical barrier.                                  |
| **Enter**            | `OnCollisionEnter(Collision collision)`                                                                      | `OnTriggerEnter(Collider other)`                                                          |
| **Stay**             | `OnCollisionStay(Collision collision)`                                                                       | `OnTriggerStay(Collider other)`                                                           |
| **Exit**             | `OnCollisionExit(Collision collision)`                                                                       | `OnTriggerExit(Collider other)`                                                           |
| **Callback Data**    | `Collision` contains contact points, relative velocity, collision impulses, and other collision information. | Callback receives the overlapping `Collider`.                                             |
| **Common Use**       | Physical interactions such as walls, floors, and objects colliding.                                          | Detection zones, pickups, hazards, and interaction areas.                                 |
| **Example**          | —                                                                                                            | Temporarily parent a player to a moving platform: `other.transform.SetParent(transform);` |
| **Leaving Trigger**  | —                                                                                                            | Remove the parent: `other.transform.SetParent(null);`                                     |


---

### 🚀 Component Retrieval

| Approach                 | Purpose                                             | Example                                                     | Key Guidance                                                                                                                  |
| ------------------------ | --------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **`[RequireComponent]`** | Declare components that a script **requires**.      | `[RequireComponent(typeof(Rigidbody))]`                     | Unity automatically adds the required component when the script is added to a GameObject. Helps prevent missing dependencies. |
| **Cache Component**      | Store a component reference for repeated use.       | `_rigidBody = GetComponent<Rigidbody>();`                   | Retrieve once, typically in `Awake()`, then reuse the cached reference.                                                       |
| **`TryGetComponent`**    | Safely retrieve a component that **may not exist**. | `if (other.TryGetComponent<Rigidbody>(out var rb)) { ... }` | Useful for components on **other objects** when the dependency is optional or uncertain.                                      |

---

# 🧪 Scenario-Based Practice

## Question 7

A player should move at a consistent speed using a Dynamic Rigidbody. Which approach is correct?

- **A.** Set linearVelocity to the desired movement speed.
- **B.** Set linearVelocity to the desired movement speed multiplied by Time.deltaTime.
- **C.** Set linearVelocity to the desired movement speed multiplied by the frame rate.
- **D.** Use transform.position so the Rigidbody moves independently of physics.

**Correct Answer: A**

**Why:** linearVelocity represents a rate of movement in units per second, so the desired velocity should be assigned directly. Time.deltaTime is used when calculating frame-based displacement, not when directly assigning velocity.

---

## Question 8

A collectible should disappear and award points when the player walks through it. Instead, the player hits the collectible as though it were a solid wall.

What was most likely omitted?
- **A.** The player's Rigidbody was made Kinematic.
- **B.** `OnCollisionEnter` was used instead of `OnDisable`.
- **C.** The script did not derive from `CSG.Physics`.
- **D.** The collectible's Collider did not have `isTrigger` enabled.

**Correct Answer: D**

**Why:** A standard Collider is solid by default. Setting `isTrigger = true` changes it into an overlap volume and allows `OnTriggerEnter()` to detect the interaction.

---

## Module 5: Events 

### 🧠 Observer Pattern Architecture

The **Observer Pattern** establishes a **one-to-many relationship** between a publisher and its subscribers.

* **Subject / Publisher** — broadcasts an event when something happens.
* **Observers / Subscribers** — listen for the event and perform their own responses.
* **Decoupling** — the publisher does not need to know which objects are listening or what they will do.

```text
             ┌─────────────────────────┐
             │   YouTuber (Publisher)  │
             │ Event: LiveStreamNotify │
             └────────────┬────────────┘
                          │
                     Broadcast
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ Gregory  │    │ Samantha │    │ Valerie  │
    │Subscriber│    │Subscriber│    │Subscriber│
    └──────────┘    └──────────┘    └──────────┘
```

---

## 📡 C# Event Publishers

The `event` keyword restricts event invocation to the class that declares the event.

```csharp
public class YouTuber : MonoBehaviour
{
    public static event Action LiveStreamNotification;

    private void StartLiveStream()
    {
        LiveStreamNotification?.Invoke();
    }
}
```

#### ⚡ Null-Conditional Event Invocation

Use `?.Invoke()` to safely invoke an event only when subscribers exist:

```csharp
OnTimerCompleted?.Invoke();
```

---

### 🔄 Event Subscribers

Subscribe in `OnEnable()` and unsubscribe in `OnDisable()` so event listeners only remain registered while active.

```csharp
private void OnEnable()
{
    YouTuber.LiveStreamNotification += HandleEventResponse;
}

private void OnDisable()
{
    YouTuber.LiveStreamNotification -= HandleEventResponse;
}
```

Failing to unsubscribe can leave callbacks referencing destroyed Unity objects, causing unnecessary references or `MissingReferenceException` errors.

---

# 🧪 Scenario-Based Practice
### Question 9

A `Door` object raises an event whenever it opens. Several other objects need to respond to the door opening, but the `Door` should not need references to those objects.

Which design best supports this requirement?

* **A.** Give the `Door` a reference to every object that needs to respond.
* **B.** Have each object check the `Door` every frame.
* **C.**  Use an event that interested objects can subscribe to.
* **D.** Create a separate `Door` class for every object that responds.

**Correct Answer: C**

**Why:** An event allows multiple objects to respond without the `Door` needing to know which objects are listening. This keeps the publisher and subscribers decoupled.

---

## Question 10

A countdown timer must trigger UI animation and audio when it reaches zero.

Which design is most appropriate?

- **A.** Have `Timer.cs` directly reference UI and audio components.
- **B.** Declare `public event Action OnTimerCompleted;`, invoke it when the timer finishes, and allow UI/audio components to subscribe.
- **C.** Use `GameObject.Find()` every frame.
- **D.** Inherit from both UI and Audio classes.

**Correct Answer: B**

**Why:** The timer broadcasts what happened without needing to know which systems respond to it.

---

# 📋 Quick-Reference Summary

| Domain               | Key Concept         | Rules & Syntax                                                                                                |
| -------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Git Workflow**     | Lifecycle           | Working Directory → Stage → Commit → Push → Pull Request                                                      |
| **Git Workflow**     | Binary Assets       | Use Git LFS for large binary assets such as FBX, audio, and textures. Configure tracking in `.gitattributes`. |
| **C# Standards**     | Encapsulation       | Keep fields private (`_speed`); expose controlled access through properties (`Speed`).                        |
| **C# Standards**     | Inspector Exposure  | Use `[SerializeField]` to expose private fields without making them public.                                   |
| **C# Standards**     | Property Validation | Reassign serialized fields through properties during initialization when validation is required.              |
| **C# Standards**     | Design Principles   | SRP, DRY, KISS, YAGNI                                                                                         |
| **Observer Pattern** | Subscription        | Subscribe with `+=` in `OnEnable()`.                                                                          |
| **Observer Pattern** | Unsubscription      | Unsubscribe with `-=` in `OnDisable()`.                                                                       |
| **Observer Pattern** | Invocation          | Use `OnEvent?.Invoke()` to safely invoke an event with zero or more subscribers.                              |
| **Unity Lifecycle**  | Initialization      | `Awake()` → `OnEnable()` → `Start()`                                                                          |
| **Unity Lifecycle**  | Recurring Updates   | `FixedUpdate()` for physics; `Update()` for frame-based logic.                                                |
| **Unity Lifecycle**  | Cleanup             | `OnDisable()` → `OnDestroy()`                                                                                 |
| **Transform Math**   | Displacement        | `Speed × Time.deltaTime × Direction.normalized`                                                               |
| **Transform Math**   | Direction           | Normalize direction vectors when constant movement speed is required.                                         |
| **Transform Math**   | Rotation            | Euler angles are human-readable; Unity uses Quaternions internally for rotation.                              |
| **Physics**          | Dynamic Rigidbody   | PhysX-driven; responds to gravity, forces, and collisions.                                                    |
| **Physics**          | Kinematic Rigidbody | Script/animation-driven; use `MovePosition()` for physics-aware movement.                                     |
| **Physics**          | Static Collider     | Collider without a Rigidbody; used for unmoving scene geometry.                                               |
| **Physics**          | Velocity            | `linearVelocity` is already measured in units per second; do not multiply by `Time.deltaTime`.                |
| **Physics**          | Collision           | Solid interaction; `OnCollisionEnter()`.                                                                      |
| **Physics**          | Trigger             | Overlap interaction; `isTrigger = true` and `OnTriggerEnter()`.                                               |
| **Physics**          | Performance         | Cache components such as `Rigidbody` rather than repeatedly calling `GetComponent()`.                         |

---

# 🎯 What to Know for the Exam

Students should be prepared to **apply**, not simply define, the concepts in this study guide.

Be prepared to:

1. **Identify the correct Git workflow** for a development scenario.
2. **Determine when Git LFS is required** and understand why.
3. **Choose an appropriate branch type** for a development task.
4. **Identify encapsulation problems** in C# code.
5. **Determine when a property setter is bypassed by Unity serialization.**
6. **Apply SRP, DRY, KISS, and YAGNI** to code and architecture decisions.
7. **Identify appropriate naming conventions and namespace structures.**
8. **Explain how the Observer Pattern reduces coupling.**
9. **Identify correct event subscription and unsubscription locations.**
10. **Diagnose static-event lifecycle problems.**
11. **Choose between `Update()` and `FixedUpdate()`.**
12. **Calculate or reason about frame-rate-independent movement.**
13. **Explain vector magnitude and normalization.**
14. **Distinguish World Space from Local Space.**
15. **Explain the difference between Euler angles and Quaternions.**
16. **Choose the correct Rigidbody type for a scenario.**
17. **Distinguish Transform movement from Rigidbody movement.**
18. **Determine when `Time.deltaTime` should and should not be used.**
19. **Distinguish Collision events from Trigger events.**
20. **Identify common physics performance problems and architectural anti-patterns.**

> ### 🧠 Exam Strategy
>
> When presented with code or a development scenario, ask:
>
> **What is the system trying to accomplish?**
> **What principle or rule applies?**
> **What is the code actually doing?**
> **Where do those two differ?**
>
> The correct answer will often be the one that preserves **encapsulation, modularity, decoupling, maintainability, performance, and predictable behavior**.
