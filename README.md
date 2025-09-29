# Unity C# Coding Standards

## Introduction

These coding standards represent recommendations born from 20 years of game development experience. They are opinions I've formed and refined throughout my career, and they continue to evolve as I learn and as the industry progresses.

**An Important Note on Adaptation:**

When working as part of an established team, **it is crucial to adapt to the team's existing coding standards**, even if they differ from these recommendations. Consistency within a codebase is more valuable than individual preference. However, when provided with an opportunity to set standards, provide guidance, or start fresh on a new project, these are the ever-evolving standards that I hold myself to.

These standards are shared openly in the hope that they may be useful to others, but they should be adapted and modified to suit your team's needs and context.

## Foundation

These coding standards are based on [Microsoft's C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions) and [.NET Naming Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/naming-guidelines), with specific adaptations for Unity development and team preferences.

## Quick Reference

### Naming Conventions
- **Classes/Structs/Methods**: `PascalCase`
- **Interfaces**: `IPascalCase` (prefix with `I`)
- **Public properties**: `PascalCase`
- **Private fields**: `_camelCase` (underscore prefix)
- **Parameters/local variables**: `camelCase`
- **Constants**: `PascalCase`

### Formatting
- **Indentation**: Tabs preferred (maintain codebase consistency)
- **Access modifiers**: Always explicit (`private`, `public`, etc.)
- **Type inference**: Use `var` when type is clear from context
- **Attributes**: Inline with properties, above methods
- **Brace style**: Non-cuddled/Allman style (opening brace on new line)
- **Braces**: Always use for multi-line blocks; omit only for single-line guard clauses
- **Using statements**: Prefer `using var` declarations over block-scoped

### Code Organization
- **Member ordering**: Public → Serialized protected → Serialized private → Protected → Private
- **Never nester**: Favor early returns, guard clauses, and abstracted methods
- **SOLID principles**: Emphasize Single Responsibility Principle
- **Readability over performance**: Favor clarity where complexity has interpretation cost
- **Protected virtual**: Use for extension points in immutable codebases and Unity lifecycle methods

### Unity Guidelines
- **Component assignment**: Prefer Inspector assignment over GetComponent
- **Component communication**: Choose pattern based on context (optional vs critical)
- **Messaging systems**: Use sparingly - prefer direct communication or intermediaries
- **Async/await**: Prefer over coroutines
- **UniTask**: Use for async operations with Unity lifecycle integration
- **DOTween**: Use with UniTask extension for awaitable tweens
- **ServiceKit**: Use Service Locator pattern for dependency management
- **Cancellation tokens**: Link with `destroyCancellationToken` for lifecycle management
- **Assembly definitions**: Use for features, packages, and logical code modules
- **SerializeField**: Use `[field: SerializeField]` for auto-property backing fields

### Git Workflow
- **Main branches**: `main` (production), `develop` (work in progress)
- **Feature branches**: `feat/feature-name` from `develop` (all lowercase)
- **Release branches**: `release/X.Y.Z` from `develop`
- **Hotfix branches**: `hotfix/X.Y.Z` from `main`
- **Fix branches**: `fix/bug-name` from `develop` (pre-production only)
- **Branch sync**: Merge `develop` into feature before PR
- **PR merging**: Squash and merge to `develop` (regular merge for large multi-part features)
- **Commit messages**: Imperative mood, descriptive, include ticket IDs

### Commenting
- **Prefer self-documenting code**: Expressive names and clear structure
- **Comment when**: Code is complex, refactoring isn't feasible, or explaining non-obvious logic
- **XML docs**: Use for public APIs and complex methods

---

## Table of Contents

- [Naming Conventions](#naming-conventions)
- [Formatting and Layout](#formatting-and-layout)
- [Code Organization](#code-organization)
- [Unity-Specific Guidelines](#unity-specific-guidelines)
- [Unity Project Organization](#unity-project-organization)
- [Commenting and Documentation](#commenting-and-documentation)
- [Version Control](#version-control)
- [AI-Assisted Development (Vibe Coding)](#ai-assisted-development)

---

## Naming Conventions

Following Microsoft's naming guidelines with Unity-specific adaptations.

### Classes, Structs, and Interfaces

- **PascalCase** for all type names
- Prefix interfaces with `I`
- Use descriptive, noun-based names

```csharp
public class PlayerController { }
public struct PlayerStats { }
public interface IHealthSystem { }
```

### Methods

- **PascalCase** for all method names
- Use verb-based names that describe the action

```csharp
public void CalculateDamage() { }
public async UniTask LoadSceneAsync(CancellationToken cancellationToken) { }
```

### Properties and Fields

- **PascalCase** for public properties
- **camelCase** for private fields
- Prefix private fields with underscore `_`
- **PascalCase** for public fields (discouraged, prefer properties)
- Use `[field: SerializeField]` to expose auto-property backing fields in Unity Inspector

```csharp
public int MaxHealth { get; private set; }
private int _currentHealth;
private readonly Transform _transform;

// Unity Inspector - exposing auto-property backing field
[field: SerializeField] public int StartingHealth { get; private set; }
```

### Parameters and Local Variables

- **camelCase** for all parameters and local variables
- Use descriptive names; favor clarity over brevity

```csharp
public void ApplyDamage(int damageAmount, DamageType damageType)
{
	int remainingHealth = _currentHealth - damageAmount;
}
```

### Constants

- **PascalCase** for constants
- Use `const` for compile-time constants, `static readonly` for runtime constants

```csharp
private const int MaxPlayers = 4;
private static readonly Vector3 SpawnPosition = new Vector3(0, 1, 0);
```

### Naming Philosophy

**Favor expressiveness with preference for concise names, but accept verbose names where they add value.**

```csharp
// Good - concise and clear
public void Jump() { }

// Good - verbose but adds clarity
public async UniTask WaitForAnimationCompleteAsync(CancellationToken cancellationToken) { }

// Avoid - unnecessarily verbose
public void JumpPlayerCharacterVertically() { }
```

---

## Formatting and Layout

### Indentation

**Preference: Tabs over spaces** ⚠️ *Differs from Microsoft standard (4 spaces)*

- Tabs are preferred but not mandatory
- **Once a standard is established in a codebase, maintain consistency**
- Configure your IDE to match the project's existing style

**Note on ongoing debate:**

This is an area of continued self-questioning. Tabs are preferred for visual clarity and historical efficiency (though compiler optimization has largely eliminated performance differences). However, spaces offer more consistent rendering across editors and tools. Additionally, LLMs commonly default to spaces, with many struggling to consistently use tabs when generating code.

**Ultimately:** Consistency within a project trumps personal preference. Adapt to the established standard.

### Access Modifiers

**Always use explicit access modifiers** ⚠️ *Stricter than Microsoft standard*

```csharp
// Good - explicit intent
private int _health;
public string Name { get; private set; }
private void UpdateHealth() { }

// Avoid - implicit access modifier
int _health;
```

**Rationale:** Highlights coder intent and maintains readability through consistency.

### Attributes

- **Inline with properties**
- **Above methods on separate line**

```csharp
// Properties - inline
[SerializeField] private int _maxHealth;
[field: SerializeField] public int CurrentHealth { get; private set; }

// Methods - above on separate line
[ContextMenu("Reset Health")]
private void ResetHealth()
{
	CurrentHealth = _maxHealth;
}
```

### Type Inference with `var`

**Prefer `var` where type is clear from context**

```csharp
// Good - type is obvious
var player = new PlayerController();
var position = new Vector3(0, 1, 0);
var enemies = new List<Enemy>();

// Avoid - type unclear
var data = GetData(); // What type is this?

// Good - explicit when unclear
PlayerData data = GetData();
Complex<Nested<Generic>> result = CalculateComplexResult();
```

**Rationale:** Simplifies refactoring when types change and removes duplicate information. Improves readability by avoiding repeated type names of varying lengths.

**Note:** While IDEs display inferred types on hover, consider explicit types when the inferred type is complex or unclear, particularly for code reviews conducted outside the IDE (terminal, GitHub, etc.).

### Brace Style (The Great Cuddley vs Non-Cuddley Debate)

**Use non-cuddley braces (Allman style) - opening braces on their own line.**

This one brings back memories! My former co-founder Dean and I had countless debates about this. He was firmly in the cuddley camp (K&R style), while I've always been team non-cuddley. We'd literally spend lunch breaks arguing about whether braces should "cuddle" up to their statements or stand proudly on their own lines.

The non-cuddley style places opening braces on a new line, aligning them vertically with their closing braces. For me, this creates clearer visual boundaries—I can scan down the left margin and immediately see where blocks begin and end.

```csharp
// My preference - non-cuddley (Allman style)
if (health <= 0)
{
	Die();
	return;
}

public void ProcessPlayer(Player player)
{
	if (player == null) 
	{
		return;
	}
	
	UpdatePlayerState(player);
}

// Dean's preference - cuddley braces (K&R style)
if (health <= 0) {
	Die();
	return;
}

public void ProcessPlayer(Player player) {
	if (player == null) {
		return;
	}
	
	UpdatePlayerState(player);
}
```

**Why I'm Team Non-Cuddley (Despite Dean's Historical Point):**

Dean's argument was that dropping brackets onto a new line could cause bugs—and he was actually referring to a real issue. In JavaScript, there's a genuine technical problem with non-cuddley braces due to Automatic Semicolon Insertion (ASI):

```javascript
// JavaScript - This breaks with non-cuddley!
return
{
    success: true
};
// JavaScript inserts a semicolon after 'return', returning undefined!

// Must use cuddley style in JavaScript for this case
return {
    success: true
};
```

This wasn't some theoretical edge case—it was a real bug that bit many developers. Languages like Go actually *enforce* cuddley braces partly because of lessons learned from JavaScript's ASI issues.

But here's the thing—we're writing C#, not JavaScript. C# doesn't have ASI, so this particular issue doesn't exist. After years of this debate (and trying both styles extensively), I've stuck with non-cuddley for a simple reason: when I'm exhausted at the end of a long coding session, those aligned braces are like visual anchors. I can immediately see the structure of the code without having to parse where blocks begin and end.

The truth is, both work in C#. This is one of those debates where context matters—use cuddley in JavaScript to avoid bugs, but in C#, pick based on readability preference and stick with it consistently.

### Member Ordering by Access Level

**Order class members by access level to emphasize the public API first.**

Organize properties and methods in the following order:
1. **Public** members (the class's public API, including serialized public properties)
2. **Serialized protected** fields (Inspector-exposed and inheritable)
3. **Serialized private** fields (Inspector-exposed but internal)
4. **Protected** members (extension points for derived classes)
5. **Private** members (internal implementation details)

Within each access level, group similar members together (properties, then methods). Serialized fields maintain the access hierarchy even though they're exposed in the Inspector.

```csharp
public class PlayerController : MonoBehaviour
{
	// 1. Public properties and fields (API surface)
	public int MaxHealth { get; private set; } = 100;
	public float MoveSpeed { get; set; } = 5f;
	[field: SerializeField] public int StartingHealth { get; private set; }
	
	// 2. Serialized protected fields (Inspector-exposed, inheritable)
	[SerializeField] protected float _jumpForce = 10f;
	[SerializeField] protected LayerMask _groundLayers;
	
	// 3. Serialized private fields (Inspector-exposed, internal)
	[SerializeField] private GameObject _playerModel;
	[SerializeField] private AudioClip _jumpSound;
	[SerializeField] private Rigidbody _rigidbody;
	
	// 4. Protected members (for inheritance)
	protected virtual float DamageMultiplier => 1.0f;
	
	// 5. Private fields (internal state)
	private int _currentHealth;
	private bool _isGrounded;
	
	// 6. Public methods (API)
	public void TakeDamage(int damage)
	{
		_currentHealth = Mathf.Max(0, _currentHealth - damage);
		OnDamageTaken(damage);
	}
	
	public void Jump()
	{
		if (!_isGrounded) return;
		_rigidbody.AddForce(Vector3.up * _jumpForce, ForceMode.Impulse);
	}
	
	// 7. Protected methods (extensibility points)
	protected virtual void Start()
	{
		_currentHealth = StartingHealth;
	}
	
	protected virtual void OnDamageTaken(int damage)
	{
		// Override point for derived classes
	}
	
	// 8. Private methods (implementation details)
	private void UpdateGroundedState()
	{
		_isGrounded = Physics.Raycast(transform.position, Vector3.down, 1.1f);
	}
	
	private void PlayJumpSound()
	{
		if (_jumpSound != null)
		{
			AudioSource.PlayClipAtPoint(_jumpSound, transform.position);
		}
	}
}
```

**Rationale:** 
- **Public-first approach** makes the class's API immediately visible to consumers
- **Serialized fields follow public** because they're part of the class's Unity Inspector interface (quasi-public)
- **Protected members before private** maintains the hierarchy of accessibility
- **Implementation details last** keeps focus on the interface rather than internals
- This ordering creates a natural flow from "what the class does" (public API) to "how it does it" (private implementation)

### Regions (Avoid)

**Avoid using `#region` directives - they often indicate violations of Single Responsibility Principle.**

Regions are frequently used to hide complexity and group related functionality within a class. However, if a class needs regions to organize its code, it's likely doing too much. Each region typically represents a different responsibility that should be extracted into its own class.

```csharp
// Avoid - regions hiding multiple responsibilities
public class PlayerController : MonoBehaviour
{
	#region Health Management
	private int _health;
	public void TakeDamage(int amount) { } // Simplified for example
	public void Heal(int amount) { } // Simplified for example
	private void Die() { } // Simplified for example
	#endregion
	
	#region Movement
	private Vector3 _velocity;
	public void Move(Vector3 direction) { } // Simplified for example
	public void Jump() { } // Simplified for example
	private void ApplyGravity() { } // Simplified for example
	#endregion
	
	#region Inventory
	private List<Item> _items;
	public void AddItem(Item item) { } // Simplified for example
	public void RemoveItem(Item item) { } // Simplified for example
	private void SortInventory() { } // Simplified for example
	#endregion
}

// Good - separate classes for separate responsibilities
public class PlayerController : MonoBehaviour
{
	[SerializeField] private HealthSystem _healthSystem;
	[SerializeField] private MovementController _movement;
	[SerializeField] private InventorySystem _inventory;
}

public class HealthSystem : MonoBehaviour
{
	private int _health;
	
	public void TakeDamage(int amount)
	{
		// Implementation
	}
	
	public void Heal(int amount)
	{
		// Implementation
	}
	
	private void Die()
	{
		// Implementation
	}
}

public class MovementController : MonoBehaviour
{
	private Vector3 _velocity;
	
	public void Move(Vector3 direction)
	{
		// Implementation
	}
	
	public void Jump()
	{
		// Implementation
	}
	
	private void ApplyGravity()
	{
		// Implementation
	}
}
```

**Code Smell Indicators:**

When you find yourself wanting to use regions, consider these alternatives:

1. **Multiple functional regions** → Extract each region into its own class
2. **"Private Methods" region** → Consider if methods belong in helper classes
3. **"Properties" region** → Use proper member ordering instead
4. **"Unity Callbacks" region** → These should be naturally grouped by member ordering
5. **Interface implementation regions** → Each interface might warrant its own class

**Rare Acceptable Uses:**

In very limited cases, regions might be acceptable:

```csharp
// Acceptable - grouping generated or required boilerplate
public partial class GeneratedClass
{
	#region Auto-Generated Code - Do Not Modify
	// Generated code from tools
	#endregion
}

// Acceptable - platform-specific implementations
public class CrossPlatformService
{
	#if UNITY_EDITOR
	// Editor-only implementation
	#elif UNITY_IOS
	// iOS-specific implementation  
	#elif UNITY_ANDROID
	// Android-specific implementation
	#endif
}
```

**Rationale:**
- **Regions hide complexity** rather than addressing it
- **Violates SRP** - Multiple regions usually mean multiple responsibilities
- **Impedes navigation** - Code folding hides important details
- **False organization** - Provides illusion of structure without actual architectural improvement
- **Better alternatives exist** - Proper class design, member ordering, and extraction eliminate the need

If your class is large enough to "need" regions, it's large enough to be refactored into smaller, focused classes.

### Control Flow Statements

**Always use braces for multi-line blocks. Omit only for single-line guard clauses.**

```csharp
// Good - single line guard clause
if (health <= 0) return;
if (!isActive) continue;

// Good - braced blocks for multi-statement or complex logic
if (health <= 0)
{
	Die();
}

// Good - both if and else have braces
if (isAlive)
{
	UpdateHealth();
}
else
{
	HandleDeath();
}

// Avoid - inconsistent bracing
if (isAlive)
	UpdateHealth();
else
{
	HandleDeath();
	ResetStats();
}
```

### Return Statements

**Inline returns for short guard clauses. Use closure for complex returns.** ⚠️ *Personal preference*

```csharp
// Good - inline guard clauses
if (target == null) return;
if (!IsValid(data)) return false;

// Good - complex returns in closure
if (complexCondition)
{
	return CalculateComplexValue();
}

// Avoid - standalone return on own line after complex logic
if (complexCondition)
	return CalculateComplexValue();
```

**Rationale:** Inline guard clauses allow multiple one-line guards to be compacted into a readable, aligned block (which could itself be abstracted into a guard method). Use closures for complex guards that need space for context, comments, or easier breakpointing.

### Using Statements

**Prefer using declarations (non-block scoped) over using statements with braces.**

```csharp
// Good - using declaration, disposed at end of enclosing scope
public void ProcessFile(string path)
{
	using var reader = new StreamReader(path);
	var content = reader.ReadToEnd();
	ProcessContent(content);
	// reader automatically disposed here
}

// Avoid - traditional using block with braces
public void ProcessFile(string path)
{
	using (var reader = new StreamReader(path))
	{
		var content = reader.ReadToEnd();
		ProcessContent(content);
	} // reader disposed here
}
```

**Exception:** When precise disposal timing is critical (e.g., releasing file locks before opening another resource), use traditional using blocks for explicit control.

```csharp
// Good - when disposal timing matters
public void ProcessSequentialFiles()
{
	using (var reader = new StreamReader(path1))
	{
		ProcessFile(reader);
	} // Explicitly disposed here before next file
	
	using (var reader = new StreamReader(path2))
	{
		ProcessFile(reader);
	}
}
```

**Rationale:** Reduces nesting and improves readability. The disposable is automatically disposed at the end of the enclosing scope. Functional requirements trump formatting preferences when disposal timing is important.

---

## Code Organization

### Never Nester Philosophy

**Favor early returns, guard clauses, and abstracted methods over deeply nested code.**

```csharp
// Good - flat structure with guard clauses
public void ProcessPlayer(Player player)
{
	if (player == null) return;
	if (!player.IsActive) return;
	if (player.Health <= 0) return;
	
	UpdatePlayerState(player);
	ProcessPlayerActions(player);
}

// Avoid - deep nesting
public void ProcessPlayer(Player player)
{
	if (player != null)
	{
		if (player.IsActive)
		{
			if (player.Health > 0)
			{
				UpdatePlayerState(player);
				ProcessPlayerActions(player);
			}
		}
	}
}
```

### SOLID Principles

Follow SOLID principles with emphasis on:

**Single Responsibility Principle (SRP)** - Primary focus

- Each class should have one reason to change
- Each method should do one thing well
- Extract complex logic into well-named helper methods

```csharp
// Good - single responsibilities
public class HealthSystem
{
	public void TakeDamage(int amount)
	{
		// Implementation
	}
	
	public void Heal(int amount)
	{
		// Implementation
	}
}

public class PlayerMovement
{
	public void Move(Vector3 direction)
	{
		// Implementation
	}
	
	public void Jump()
	{
		// Implementation
	}
}

// Avoid - multiple responsibilities
public class Player
{
	public void TakeDamage(int amount) { }
	public void Move(Vector3 direction) { }
	public void SaveToDatabase() { }
	public void RenderHealthBar() { }
}
```

### Readability Over Performance

**Favor readability over minor performance gains where complexity has a cost in interpretation time.**

```csharp
// Good - readable
public bool IsPlayerEligibleForReward()
{
	var questId = _currentQuestId;
	var requiredLevel = 10;
	var rewardId = _currentRewardId;
	
	bool hasCompletedQuest = _questSystem.IsQuestComplete(questId);
	bool hasMinimumLevel = _player.Level >= requiredLevel;
	bool hasNotClaimedBefore = !_rewardSystem.HasClaimed(rewardId);
	
	return hasCompletedQuest && hasMinimumLevel && hasNotClaimedBefore;
}

// Avoid - micro-optimized but less clear
public bool IsPlayerEligibleForReward() =>
	_questSystem.IsQuestComplete(questId) && _player.Level >= requiredLevel && 
	!_rewardSystem.HasClaimed(rewardId);
```

---

## Unity-Specific Guidelines

### Component Dependencies

**Prefer Inspector assignment over GetComponent for dependencies.**

Assign component dependencies through the Unity Inspector rather than finding them at runtime with GetComponent. This approach exposes missing dependencies immediately in the editor rather than causing runtime errors.

```csharp
// Good - Inspector assignment with clear dependencies
public class PlayerController : MonoBehaviour
{
	[Header("Required Components")]
	[SerializeField] private Rigidbody _rigidbody;
	[SerializeField] private Animator _animator;
	[SerializeField] private AudioSource _audioSource;
	
	[Header("Dependencies")]
	[SerializeField] private HealthSystem _healthSystem;
	[SerializeField] private InventoryManager _inventory;
	
	private void Start()
	{
		// Components are already assigned and validated
		_rigidbody.velocity = Vector3.zero;
	}
}

// Avoid - runtime component lookup
public class PlayerController : MonoBehaviour
{
	private Rigidbody _rigidbody;
	private Animator _animator;
	private HealthSystem _healthSystem;
	
	private void Awake()
	{
		// Runtime lookups add overhead and can fail silently
		_rigidbody = GetComponent<Rigidbody>();
		_animator = GetComponent<Animator>();
		_healthSystem = GetComponentInChildren<HealthSystem>();
	}
}
```

**Benefits:**
- **Early error detection** - Missing dependencies are visible in the Inspector
- **Better performance** - No runtime GetComponent overhead
- **Clear dependencies** - Inspector shows all required components at a glance
- **Easier testing** - Dependencies can be mocked/swapped in the Inspector
- **Prefab validation** - Prefabs show missing references immediately

**Exception - Dynamic or Optional Components:**

Use GetComponent when components are truly optional or added dynamically:

```csharp
public class InteractionHandler : MonoBehaviour
{
	private void OnTriggerEnter(Collider other)
	{
		// Dynamic lookup for optional component on other object
		var interactable = other.GetComponent<IInteractable>();
		interactable?.Interact();
	}
}
```

**Runtime Validation:**

Validate references are set using OnValidate (editor) or Awake (runtime):

```csharp
public class PlayerController : MonoBehaviour
{
	[SerializeField] private Rigidbody _rigidbody;
	[SerializeField] private HealthSystem _healthSystem;
	[SerializeField] private WeaponSystem _weaponSystem;
	
	// Editor-time validation
	private void OnValidate()
	{
		if (_rigidbody == null)
		{
			Debug.LogWarning($"Rigidbody is not assigned on {gameObject.name}", this);
		}
		
		if (_healthSystem == null)
		{
			Debug.LogWarning($"HealthSystem is not assigned on {gameObject.name}", this);
		}
	}
	
	// Runtime validation with errors
	private void Awake()
	{
		ValidateReferences();
	}
	
	private void ValidateReferences()
	{
		var missingRefs = new System.Collections.Generic.List<string>();
		
		if (_rigidbody == null) missingRefs.Add(nameof(_rigidbody));
		if (_healthSystem == null) missingRefs.Add(nameof(_healthSystem));
		if (_weaponSystem == null) missingRefs.Add(nameof(_weaponSystem));
		
		if (missingRefs.Count > 0)
		{
			Debug.LogError($"Missing references on {gameObject.name}: {string.Join(", ", missingRefs)}", this);
			#if UNITY_EDITOR
			UnityEditor.EditorApplication.isPaused = true;
			#endif
		}
	}
}
```

### Component Communication Patterns

**Understand both child-to-parent and parent-to-child patterns, choosing based on context.**

Unity components can communicate through two primary patterns, each with distinct advantages. Choose based on your specific requirements for modularity, performance, and control.

#### Pattern 1: Child-to-Parent Lookup with Subscriptions

Children look up through the hierarchy to find dependencies and subscribe to changes:

```csharp
// Child component looks up and subscribes
public class PlayerColorIndicator : MonoBehaviour
{
	[SerializeField] private Renderer _renderer;
	private IPlayerState _playerState;
	private MaterialPropertyBlock _propertyBlock;
	
	private void Start()
	{
		_propertyBlock = new MaterialPropertyBlock();
		
		// Look up through hierarchy for player state
		_playerState = GetComponentInParent<IPlayerState>();
		if (_playerState != null)
		{
			// Subscribe to changes
			_playerState.OnHealthChanged += UpdateColorBasedOnHealth;
			_playerState.OnStateChanged += UpdateColorBasedOnState;
			
			// Initial update
			UpdateColorBasedOnHealth(_playerState.CurrentHealth);
		}
	}
	
	private void UpdateColorBasedOnHealth(float health)
	{
		var healthPercent = health / _playerState.MaxHealth;
		var color = Color.Lerp(Color.red, Color.green, healthPercent);
		
		_renderer.GetPropertyBlock(_propertyBlock);
		_propertyBlock.SetColor("_Color", color);
		_renderer.SetPropertyBlock(_propertyBlock);
	}
	
	private void OnDestroy()
	{
		// Unsubscribe to prevent memory leaks
		if (_playerState != null)
		{
			_playerState.OnHealthChanged -= UpdateColorBasedOnHealth;
			_playerState.OnStateChanged -= UpdateColorBasedOnState;
		}
	}
}
```

**Advantages:**
- **Modularity** - Components are self-contained and can be dropped anywhere in appropriate hierarchies
- **Open/Closed Principle** - Parents open for extension without modification
- **Dynamic hierarchies** - Runtime spawned/destroyed objects automatically participate
- **Designer-friendly** - Non-programmers can add/remove components without code changes
- **Scalability** - New reactive components require zero parent changes

**Disadvantages:**
- **Hidden dependencies** - Parent requirements not explicit in child Inspector
- **Subscription management** - Must handle unsubscribing to prevent memory leaks
- **Performance overhead** - Each child performs its own lookup
- **Debugging complexity** - Event chains can be harder to trace

**Best for:**
- UI elements reacting to game state
- Optional visual/audio feedback components
- Modular systems where components may be added/removed frequently
- Designer-configured behaviors

#### Pattern 2: Parent-to-Child Direct Control

Parent maintains references to children and directly controls them:

```csharp
// Parent directly manages children
public class WeaponSystem : MonoBehaviour
{
	[Header("Core Components")]
	[SerializeField] private ProjectileLauncher _launcher;
	[SerializeField] private AmmoDisplay _ammoDisplay;
	[SerializeField] private MuzzleFlash _muzzleFlash;
	
	private int _currentAmmo = 30;
	
	private void Start()
	{
		// Initialize all controlled components
		_ammoDisplay.SetAmmo(_currentAmmo);
		_muzzleFlash.Initialize(this);
	}
	
	public void Fire()
	{
		if (_currentAmmo <= 0) return;
		
		// Direct, ordered control of components
		_launcher.Launch(CalculateDamage());
		_muzzleFlash.Play();
		_currentAmmo--;
		_ammoDisplay.SetAmmo(_currentAmmo);
		
		// Parent controls exact execution order and can optimize
		if (_currentAmmo == 0)
		{
			_ammoDisplay.ShowEmptyWarning();
		}
	}
}
```

**Advantages:**
- **Explicit control flow** - Clear, predictable data flow from authoritative source
- **Performance optimization** - Batch updates, control order, skip unnecessary calls
- **Debugging clarity** - Stack traces show clear parent→child chains
- **Memory safety** - No subscription leaks or weak reference management
- **Guaranteed execution order** - Parent controls exact sequence of operations

**Disadvantages:**
- **Tight coupling** - Parent must know about all child types
- **Rigid hierarchies** - Adding new child types requires parent modification
- **Violates Open/Closed** - Parent needs changes for new functionality
- **Less reusable** - Children depend on specific parent implementation

**Best for:**
- Critical gameplay systems requiring precise control
- Performance-critical updates (particle systems, LOD management)
- Systems where parent owns children lifecycle completely
- Deterministic systems requiring exact execution order

#### Choosing Between Patterns

Consider these factors when selecting a communication pattern:

| Factor | Child-to-Parent | Parent-to-Child |
|--------|----------------|-----------------|
| **Component is optional** | ✅ Preferred | ❌ Avoid |
| **Need exact execution order** | ❌ Difficult | ✅ Natural |
| **Designer needs to add/remove** | ✅ Excellent | ❌ Requires setup |
| **Performance critical** | ⚠️ Consider overhead | ✅ Can optimize |
| **Component reusability** | ✅ High | ❌ Low |
| **Debugging requirements** | ⚠️ Event chains | ✅ Direct calls |
| **Memory management** | ⚠️ Must unsubscribe | ✅ Simple |

#### Hybrid Approach

Often the best solution combines both patterns:

```csharp
public class PlayerSystem : MonoBehaviour
{
	// Critical components use direct references
	[Header("Core Systems")]
	[SerializeField] private PlayerMovement _movement;
	[SerializeField] private WeaponController _weapon;
	
	// Optional components use events
	public event System.Action<float> OnHealthChanged;
	public event System.Action<PlayerState> OnStateChanged;
	
	private void UpdateHealth(float newHealth)
	{
		_health = newHealth;
		
		// Direct control for critical components
		if (_health <= 0)
		{
			_movement.DisableMovement();
			_weapon.DisableWeapons();
		}
		
		// Events for optional listeners
		OnHealthChanged?.Invoke(_health);
	}
}

// Optional UI component uses child-to-parent
public class HealthBarUI : MonoBehaviour
{
	private void Start()
	{
		var player = GetComponentInParent<PlayerSystem>();
		if (player != null)
		{
			player.OnHealthChanged += UpdateHealthBar;
		}
	}
}
```

#### Using Interfaces for Flexibility

Regardless of pattern choice, interfaces provide flexibility:

```csharp
public interface IHealth
{
	float CurrentHealth { get; }
	float MaxHealth { get; }
	event System.Action<float> OnHealthChanged;
	event System.Action OnDeath;
}

// Works with either pattern
public class HealthDisplay : MonoBehaviour
{
	[SerializeField] private bool _searchInParents = true;
	[SerializeField] private Image _healthBar;
	
	// Can be assigned in Inspector (parent-to-child)
	// or found at runtime (child-to-parent)
	private IHealth _healthSource;
	
	private void Start()
	{
		if (_healthSource == null && _searchInParents)
		{
			_healthSource = GetComponentInParent<IHealth>();
		}
		
		if (_healthSource != null)
		{
			_healthSource.OnHealthChanged += UpdateDisplay;
			UpdateDisplay(_healthSource.CurrentHealth);
		}
	}
	
	private void UpdateDisplay(float health)
	{
		_healthBar.fillAmount = health / _healthSource.MaxHealth;
	}
}
```

**Key Principle:** Choose the pattern that best matches your component's relationship and requirements. Critical, owned components benefit from direct control, while optional, modular components thrive with lookup patterns.

### Messaging Systems vs Direct Communication

**Consider carefully before implementing messaging systems - often direct communication or dedicated intermediaries are clearer.**

This is an area where my thinking has evolved significantly over the years. I was once a strong advocate for messaging systems—they felt like the "proper" architectural solution. But experience has taught me that what seems clever in theory often becomes a burden in practice. This evolution away from messaging systems toward more explicit communication patterns reflects a hard-won preference for clarity over cleverness.

#### My Journey with Messaging Systems

I'll be honest—early in my career, I thought messaging systems were the answer to everything. The promise of perfectly decoupled systems was intoxicating:

```csharp
// The allure of messaging systems - everything is decoupled!
public class GameEventBus
{
	private static Dictionary<Type, List<Delegate>> _subscribers = new();
	
	public static void Subscribe<T>(Action<T> handler)
	{
		if (!_subscribers.ContainsKey(typeof(T)))
			_subscribers[typeof(T)] = new List<Delegate>();
		_subscribers[typeof(T)].Add(handler);
	}
	
	public static void Publish<T>(T message)
	{
		if (_subscribers.TryGetValue(typeof(T), out var handlers))
		{
			foreach (Action<T> handler in handlers)
				handler?.Invoke(message);
		}
	}
}

// Usage seems simple and decoupled
public class ScoreManager : MonoBehaviour
{
	private void Start()
	{
		GameEventBus.Subscribe<EnemyKilledMessage>(OnEnemyKilled);
	}
	
	private void OnEnemyKilled(EnemyKilledMessage msg)
	{
		AddScore(msg.Points);
	}
}
```

**The problems that slowly revealed themselves:**

I remember the exact project where my faith in messaging systems started to crack. We had built this beautiful, completely decoupled system where everything communicated via messages. It felt so clean! Then came the bugs...

- **Hidden dependencies** - I spent an entire day trying to figure out why the score wasn't updating, only to discover a missing subscription three systems away
- **Debugging nightmare** - Stack traces became useless. "Who sent this message?" became my most-asked question
- **Race conditions** - The order messages were processed started to matter, but we had no control over it
- **Memory leaks** - We kept finding UI elements that were still subscribed to events long after being destroyed
- **Type proliferation** - We had `PlayerDamagedMessage`, `PlayerDamagedUIMessage`, `PlayerDamagedAudioMessage`... it was madness
- **"Magic" code** - New team members would stare at the codebase asking "but how does anything actually *happen*?"

#### The Turning Point

The real turning point came when I spent three hours debugging an issue, only to realize that the solution was to move one subscription from `Start()` to `Awake()`. That's when I knew something was wrong with the approach, not the implementation.

```csharp
// Better - Direct dependency with interface
public class ScoreManager : MonoBehaviour
{
	private IEnemySystem _enemySystem;
	
	private void Start()
	{
		_enemySystem = ServiceLocator.Get<IEnemySystem>();
		_enemySystem.OnEnemyKilled += HandleEnemyKilled;
	}
	
	private void HandleEnemyKilled(Enemy enemy, int points)
	{
		AddScore(points);
		// Clear, traceable, debuggable
	}
}
```

#### When Messaging Systems Still Make Sense

Now, I'm not saying messaging systems are always bad—that would be replacing one dogma with another. Through trial and error, I've found they still have their place:

**1. True Broadcasting (1-to-Many Unknown)**
```csharp
// Achievement system - unknown number of achievement listeners
public interface IAchievementEvent { }

public class FirstBloodAchievement : IAchievementEvent
{
	public string PlayerId { get; set; }
	public float TimeElapsed { get; set; }
}

// Multiple systems might care, but we don't know which
// Analytics, UI, Save System, Steam Integration, etc.
```

**2. Cross-Scene Communication**
```csharp
// When systems span multiple scenes
public class CrossSceneEventBus
{
	// Useful when you can't have direct references
	// But consider if ScriptableObject events might be better
}
```

**3. Modding/Plugin Architecture**
```csharp
// When external code needs to hook into your systems
public class ModEventSystem
{
	// Mods can subscribe without modifying core game
}
```

#### Finding My Sweet Spot: Service Locator + Interfaces

After years of flip-flopping between "decouple everything" and "just reference things directly," I've found what works for me. The combination of Service Locator pattern with explicit interfaces has become my go-to approach. It's not perfect, but it's pragmatic:

```csharp
// Clear contracts without tight coupling
public interface IPlayerHealth
{
	float Current { get; }
	float Max { get; }
	event Action<float> OnHealthChanged;
	event Action OnDeath;
}

public interface ICombatLog
{
	void LogDamage(GameObject source, GameObject target, float damage);
	void LogDeath(GameObject victim, GameObject killer);
}

// Systems declare what they need explicitly
public class DamageNumberUI : MonoBehaviour
{
	private ICombatLog _combatLog;
	
	private void Start()
	{
		// Explicit dependency - clear what this system needs
		_combatLog = ServiceLocator.Get<ICombatLog>();
		_combatLog.OnDamageLogged += ShowDamageNumber;
	}
}
```

**Why this approach has stuck with me:**
- **Explicit contracts** - I can see exactly what each system depends on
- **IDE navigation works** - Ctrl+Click actually takes me somewhere useful
- **Compile-time safety** - I find out about breaking changes immediately, not at runtime
- **Natural refactoring signals** - When a class needs 5+ interfaces, it's screaming "I'm doing too much!"

The last point has been particularly valuable. With messaging systems, you could subscribe to 20 different messages and it would look fine. With explicit interface dependencies, the constructor or Start method becomes a monster, and that discomfort drives better design.

#### The Dedicated Intermediary Pattern (My Favorite Discovery)

This pattern emerged naturally once I stopped fighting the "too many dependencies" signal. Instead of having systems talk to each other through messages or complex dependency chains, I now create explicit coordinators:

```csharp
// Instead of messaging between UI, Audio, VFX, and GamePlay...
public class CombatCoordinator : MonoBehaviour
{
	[Header("Systems to Coordinate")]
	[SerializeField] private CombatUI _ui;
	[SerializeField] private CombatAudio _audio;
	[SerializeField] private CombatVFX _vfx;
	[SerializeField] private CombatStats _stats;
	
	public void ProcessHit(HitInfo hit)
	{
		// Explicit orchestration - clear order of operations
		var damage = _stats.CalculateDamage(hit);
		
		_ui.ShowDamage(hit.Position, damage);
		_audio.PlayHitSound(hit.Type, hit.Position);
		_vfx.SpawnHitEffect(hit.Position, hit.Normal);
		_stats.ApplyDamage(hit.Target, damage);
		
		// Clear what happens and in what order
	}
}
```

This feels so much better than the old way. I know exactly what happens when a hit occurs, in what order, and I can debug it with a simple breakpoint.

#### My Current Guidelines (Subject to Change!)

| Scenario | Recommended Approach | Avoid |
|----------|---------------------|--------|
| **2-3 systems communicating** | Direct interfaces | Message bus |
| **UI responding to game state** | Events on interfaces | Global events |
| **System needs 5+ dependencies** | Create intermediary | Message spam |
| **Cross-scene data** | ScriptableObject events | Static message bus |
| **Unknown subscribers** | Consider messaging | Force-fitting direct calls |
| **Mod support needed** | Message bus or hooks | Direct references |

#### If You're Stuck with a Messaging System

I've been there—inheriting or having built a system where everything uses messages. Here's how I've approached gradually moving away:

1. **Start with the pain points** - Those high-traffic messages that everyone subscribes to
2. **Group related messages** - They often naturally form interfaces
3. **Refactor gradually** - I've learned the hard way not to rewrite everything at once
4. **Keep some messages** - Don't be dogmatic; some genuinely benefit from broadcasting

```csharp
// Before: Multiple related messages
PublishMessage(new PlayerDamagedMessage(damage));
PublishMessage(new PlayerHealthUpdatedMessage(health));
PublishMessage(new PlayerDiedMessage());

// After: Coherent interface
public interface IPlayerHealth
{
	event Action<DamageInfo> OnDamaged;
	event Action<float> OnHealthChanged;
	event Action<DeathInfo> OnDeath;
}
```

**A Final Thought:** 

My evolution from messaging systems to more direct communication wasn't overnight, and I still question it sometimes. There are days when I see a particularly elegant message-based solution and think "maybe I was too hasty..." But then I remember those three-hour debugging sessions, and I'm reminded why I changed my approach.

The key question I now ask myself: "If I had to debug this at 2 AM, would I thank past-me or curse past-me?" Usually, that leads me away from clever messaging and toward boringly obvious direct connections. And boring, I've learned, is often a virtue in code.

### Async/Await Over Coroutines

**Prefer async/await patterns with UniTask over traditional coroutines.**

```csharp
// Good - async/await with UniTask
public async UniTask LoadSceneAsync(CancellationToken cancellationToken)
{
	await SceneManager.LoadSceneAsync(sceneName)
		.ToUniTask(cancellationToken: cancellationToken);
}

// Avoid - coroutines for new code
public IEnumerator LoadSceneCoroutine()
{
	yield return SceneManager.LoadSceneAsync(sceneName);
}
```

### Cancellation Tokens

**Use cancellation tokens extensively with async code. Link with MonoBehaviour lifecycle.**

```csharp
public class PlayerController : MonoBehaviour
{
	private CancellationTokenSource _cts;
	
	private void Start()
	{
		// Combine with destroyCancellationToken
		_cts = CancellationTokenSource.CreateLinkedTokenSource(this.destroyCancellationToken);
		InitializeAsync(_cts.Token).Forget();
	}
	
	private async UniTask InitializeAsync(CancellationToken cancellationToken)
	{
		await LoadPlayerDataAsync(cancellationToken);
		await SetupPlayerSystemsAsync(cancellationToken);
	}
	
	private void OnDestroy()
	{
		_cts?.Cancel();
		_cts?.Dispose();
	}
}
```

**Important:** `destroyCancellationToken` becomes inaccessible during GameObject destruction. Cache or combine it as appropriate.

**Rationale:** Ensures async method chains are linked with Unity's GameObject lifecycle, preventing dangling tasks.

### Protected Virtual Members

**Use `protected virtual` intentionally for extension points in immutable codebases.**

When developing code intended for Unity packages or shared libraries that will become immutable, use `protected virtual` for methods and properties that are intended as extension points. This promotes extensibility while keeping internal implementation details private or non-virtual.

```csharp
// Good - extensible package code
public class BaseInteractable : MonoBehaviour
{
	protected virtual void OnInteract()
	{
		PlayInteractSound();
		TriggerInteractAnimation();
	}
	
	protected virtual float InteractionRange => 2.0f;
}

// Consumer can extend
public class CustomInteractable : BaseInteractable
{
	protected override void OnInteract()
	{
		base.OnInteract();
		ApplyCustomEffect();
	}
	
	protected override float InteractionRange => 5.0f;
}
```

**Use `protected virtual` for Unity lifecycle methods to signal base implementation exists.**

When a base class implements Unity lifecycle methods, use `protected virtual` instead of `private`. This avoids the need for the `new` keyword in derived classes and makes it clear that the base implementation contains important logic.

```csharp
// Good - base class signals overridable lifecycle
public class BaseController : MonoBehaviour
{
	protected virtual void Awake()
	{
		InitializeCoreSystems();
	}
	
	protected virtual void Start()
	{
		RegisterWithManager();
	}
}

// Derived class - clear override, no 'new' keyword needed
public class PlayerController : BaseController
{
	protected override void Awake()
	{
		base.Awake(); // Clear signal that base has logic
		InitializePlayerSystems();
	}
	
	protected override void Start()
	{
		base.Start();
		LoadPlayerData();
	}
}

// Avoid - private methods require 'new' keyword and hide base logic
public class BaseController : MonoBehaviour
{
	private void Awake()
	{
		InitializeCoreSystems();
	}
}

public class PlayerController : BaseController
{
	private new void Awake() // 'new' keyword hides base implementation
	{
		// No clear indication base.Awake() exists
		InitializePlayerSystems();
	}
}
```

**Rationale:** Makes inheritance chains explicit, prevents bugs from missed base calls, and ensures package code remains flexible for consumers.

### Recommended Packages

#### UniTask

Use UniTask for async/await in Unity:
- Better performance than standard Tasks
- Proper Unity lifecycle integration
- Zero allocation async operations

```csharp
public async UniTask<PlayerData> LoadPlayerAsync(CancellationToken cancellationToken)
{
	await UniTask.Delay(TimeSpan.FromSeconds(1), cancellationToken: cancellationToken);
	return new PlayerData();
}
```

#### DOTween

Use DOTween for tweening with UniTask integration:

```csharp
public async UniTask AnimateHealthBarAsync(float targetValue, CancellationToken cancellationToken)
{
	await _healthBarImage
		.DOFillAmount(targetValue, 0.3f)
		.SetEase(Ease.OutQuad)
		.ToUniTask(cancellationToken: cancellationToken);
}
```

**Enable UniTask DOTween extension** for proper async/await integration with tweens.

#### ServiceKit

Use [ServiceKit](https://github.com/PaulNonatomic/ServiceKit) for dependency management via Service Locator pattern:

```csharp
// Register services
ServiceLocator.Register<IHealthSystem>(new HealthSystem());
ServiceLocator.Register<IInventorySystem>(new InventorySystem());

// Retrieve services synchronously
var healthSystem = ServiceLocator.Get<IHealthSystem>();

// Retrieve services asynchronously (waits for registration)
var inventorySystem = await ServiceLocator.GetAsync<IInventorySystem>(cancellationToken);
```

**Why Service Locator over full IoC (Zenject/VContainer):**

- **Simpler learning curve** - Easier for evolving teams to understand and adopt
- **Faster prototyping** - Less ceremony and setup required for rapid iteration
- **Suitable for small teams/projects** - Well-suited for typical small to medium-sized projects
- **Less overhead** - Minimal configuration compared to full DI containers
- **ServiceKit async support** - Handles async dependency resolution elegantly

**When to consider full IoC solutions:**

- Large, long-lived projects with complex dependency graphs
- Teams experienced with DI patterns
- Need for advanced features (lifecycle management, auto-wiring, installers)
- When ceremony and structure benefit the project

**Rationale:** Service Locator provides sufficient dependency management for most Unity projects without the complexity and setup time of full IoC frameworks. For small teams and small to medium-sized projects, ServiceKit offers the right balance of simplicity and capability.

### Assembly Definition Files (asmdef)

**Use assembly definition files (.asmdef) to organize self-contained code modules.**

Create separate assemblies for logical groupings such as:
- Features (e.g., inventory system, dialogue system)
- Minigames or standalone gameplay modules
- Packages intended for reuse across projects
- Core systems with clear boundaries

**Structure with Runtime and Editor separation:**

```
Assets/
├── ProjectName/
│   ├── Common/
│   │   ├── Content/           # Shared assets
│   │   └── Source/
│   │       ├── Runtime/
│   │       │   ├── ProjectName.Common.Runtime.asmdef
│   │       │   └── [runtime scripts]
│   │       └── Editor/
│   │           ├── ProjectName.Common.Editor.asmdef
│   │           └── [editor scripts]
│   └── MiniGames/
│       ├── GameOne/
│       │   ├── Content/       # Feature-specific assets
│       │   └── Source/
│       │       ├── Runtime/
│       │       │   ├── ProjectName.GameOne.Runtime.asmdef
│       │       │   └── [runtime scripts]
│       │       └── Editor/
│       │           ├── ProjectName.GameOne.Editor.asmdef
│       │           └── [editor scripts]
│       └── GameTwo/
│           ├── Content/       # Feature-specific assets
│           └── Source/
│               ├── Runtime/
│               │   ├── ProjectName.GameTwo.Runtime.asmdef
│               │   └── [runtime scripts]
│               └── Editor/
│                   ├── ProjectName.GameTwo.Editor.asmdef
│                   └── [editor scripts]
```

**Assembly naming convention:**
- Format: `CompanyName.ProjectName.FeatureName.Runtime.asmdef`
- Examples: 
  - `IGB.BoxParty.Basketball.Runtime.asmdef`
  - `IGB.BoxParty.Basketball.Editor.asmdef`
  - `CompanyName.GameName.Common.Runtime.asmdef`

**Benefits:**
- **Reduced compilation times** - Unity only recompiles changed assemblies
- **Enforced modularity** - Explicit dependencies prevent tight coupling
- **Reusability** - Self-contained modules can be moved between projects
- **Clear architecture** - Assembly boundaries document system boundaries
- **Structural discipline** - Forces consideration of separation of concerns during project assembly
- **Runtime/Editor separation** - Editor code automatically excluded from builds

**When to use:**
- **Recommended** for packages, reusable features, and established system boundaries
- **Good practice** even in early projects to aid in assembling clear structure and marking separation of concerns
- **Can be premature** in rapid prototyping phases where architecture is highly fluid and boundaries are unclear

**Handling Circular Dependencies:**

When two assemblies need to reference each other, introduce a third intermediary assembly that both can reference, similar to breaking circular references in architecture patterns.

```csharp
// Problem: Circular dependency
// Player.Runtime.asmdef needs Enemy
// Enemy.Runtime.asmdef needs Player

// Solution: Create shared interface assembly
// Common/Source/Runtime/Interfaces/ (in Common.Runtime.asmdef)
public interface IDamageable
{
	void TakeDamage(int amount);
}

public interface IAttacker
{
	int AttackPower { get; }
}

// Player/Source/Runtime/ (Player.Runtime.asmdef references Common.Runtime)
public class Player : MonoBehaviour, IDamageable, IAttacker
{
	public void TakeDamage(int amount) { }
	public int AttackPower => 10;
}

// Enemy/Source/Runtime/ (Enemy.Runtime.asmdef references Common.Runtime)
public class Enemy : MonoBehaviour, IDamageable, IAttacker
{
	public void TakeDamage(int amount) { }
	public int AttackPower => 5;
}
```

**Rationale:** Assembly definitions help maintain clean architecture and improve iteration times as projects scale. Separating Runtime and Editor assemblies ensures clean builds and prevents accidental editor dependencies in runtime code.

---

## Unity Project Organization

### Folder Structure

Maintain a clear, consistent folder structure that separates concerns and makes code discoverable.

#### Top-Level Organization

```
Assets/
├── ProjectName/              # Root namespace folder
│   ├── Common/              # Shared code and assets
│   ├── MiniGames/           # Individual features/minigames
│   ├── Wrapper/             # Meta-game or shell
│   └── ...
├── Configuration/           # Project settings, resources
├── Plugins/                 # Third-party packages
└── StreamingAssets/         # Runtime data files
```

#### Feature/Minigame Structure

Each feature or minigame should be self-contained with its own directory structure:

```
MiniGames/
└── FeatureName/            # e.g., Basketball, Inventory, DialogueSystem
    ├── Content/            # All assets for this feature
    │   ├── Scenes/        # Unity scenes
    │   ├── Prefabs/       # Prefabs specific to this feature
    │   ├── Materials/     # Materials
    │   ├── Textures/      # Textures and sprites
    │   ├── Audio/         # Audio files
    │   ├── Models/        # 3D models
    │   └── Animations/    # Animation clips and controllers
    ├── Source/            # All code for this feature
    │   ├── Runtime/       # Runtime scripts
    │   │   ├── FeatureName.Runtime.asmdef
    │   │   ├── Controllers/
    │   │   ├── Services/
    │   │   ├── UI/
    │   │   └── ...
    │   └── Editor/        # Editor-only scripts
    │       └── FeatureName.Editor.asmdef
    ├── README.md          # Feature documentation
    └── IMPROVEMENTS.md    # Known issues and future work
```

#### Common/Shared Code Structure

```
Common/
├── Content/               # Shared assets
│   ├── Prefabs/          # Reusable prefabs
│   ├── Materials/        # Shared materials
│   ├── Fonts/            # Project fonts
│   ├── Textures/         # Shared textures
│   └── Models/           # Shared models
└── Source/               # Shared code
    ├── Runtime/          # Shared runtime code
    │   ├── Common.Runtime.asmdef
    │   ├── Audio/
    │   ├── UI/
    │   ├── Extensions/
    │   ├── Utils/
    │   └── ...
    └── Editor/           # Shared editor code
        └── Common.Editor.asmdef
```

### Assembly Definition Structure

**Each feature should have separate assemblies for Runtime and Editor code:**

```
Source/
├── Runtime/
│   ├── IGB.BoxParty.Basketball.Runtime.asmdef
│   └── [runtime scripts]
└── Editor/
    ├── IGB.BoxParty.Basketball.Editor.asmdef  (references Runtime)
    └── [editor scripts]
```

**Benefits:**
- Clear separation of runtime vs editor code
- Editor assemblies automatically excluded from builds
- Runtime assemblies can be tested independently
- Prevents accidental editor code dependencies in runtime

### Naming Conventions

#### Folders
- **PascalCase** for all folder names
- Descriptive, singular or plural as appropriate
- Avoid abbreviations except for common terms (UI, FX, AI)

```
✓ Scripts/
✓ Prefabs/
✓ PlayerControllers/
✗ scripts/
✗ stuff/
✗ misc/
```

#### Scenes
- **PascalCase** with descriptive names
- Include context when helpful

```
✓ MainMenu.unity
✓ BasketballMinigame.unity
✓ Level01_Forest.unity
✗ scene1.unity
✗ test.unity
✗ Untitled.unity
```

#### Prefabs
- **PascalCase** with descriptive names
- Include type suffix when helpful (UI, Service, Manager)

```
✓ PlayerCharacter.prefab
✓ InventoryUI.prefab
✓ AudioService.prefab
✓ EnemySpawner.prefab
✗ player.prefab
✗ Prefab1.prefab
```

#### Materials & Textures
- **PascalCase** with descriptive names
- Include purpose/type in name

```
Materials:
✓ PlayerSkin_Default.mat
✓ Glass_Transparent.mat
✓ Metal_Brushed.mat

Textures:
✓ PlayerSkin_Diffuse.png
✓ PlayerSkin_Normal.png
✓ UI_Button_Idle.png
```

### Documentation in Feature Folders

Each self-contained feature should include:

**README.md** - Feature overview and usage:
```markdown
# Basketball Minigame

## Overview
Brief description of the feature/minigame

## Setup
How to use/integrate this feature

## Dependencies
What this feature requires

## Key Components
Main classes and their purposes
```

**IMPROVEMENTS.md** - Technical debt and future work:
```markdown
# Improvements & Known Issues

## Known Issues
- Issue description and workaround

## Future Improvements
- Potential enhancements
- Performance optimizations needed
```

### Best Practices

**Self-Contained Features:**
- Each feature should be **moveable** between projects
- Minimize dependencies on project-specific code
- Use interfaces and dependency injection for external dependencies
- Keep all related assets in the feature's Content folder

**Clear Separation:**
- **Content** = Assets (scenes, prefabs, materials, etc.)
- **Source/Runtime** = Runtime code with .asmdef
- **Source/Editor** = Editor-only code with .asmdef

**Avoid:**
- Deeply nested folder structures (>4-5 levels)
- Generic folders like "Misc", "Stuff", "Temp" in commits
- Mixing unrelated features in the same folder
- Putting scripts directly in Assets root

**Configuration & Settings:**
- Store project settings in dedicated `Configuration/` folder
- Use ScriptableObjects for game configuration
- Keep Resources folder minimal (Addressables preferred)

---

## Commenting and Documentation

### Self-Documenting Code

**Prefer self-documenting code where names and methods contain explanation.**

```csharp
// Good - code explains itself
public void ApplyFallDamage()
{
	if (!HasFallenFarEnough()) return;
	
	int damage = CalculateFallDamage();
	_healthSystem.TakeDamage(damage);
}

// Avoid - unnecessary comments
public void ApplyFallDamage()
{
	// Check if fallen far enough
	if (!HasFallenFarEnough()) return;
	
	// Calculate the damage
	int damage = CalculateFallDamage();
	// Apply damage to health system
	_healthSystem.TakeDamage(damage);
}
```

### When to Comment

**Discourage over-commenting and needless commenting. Comment when:**

- Code is complex and lacks context
- Refactoring for readability isn't feasible
- Explaining non-obvious business logic or Unity-specific quirks
- Warning about potential issues or edge cases

```csharp
// Good - explains non-obvious Unity behavior
// Unity's destroyCancellationToken becomes inaccessible during OnDestroy,
// so we cache it during Awake to ensure cleanup can cancel properly
private CancellationToken _cachedDestroyToken;

private void Awake()
{
	_cachedDestroyToken = this.destroyCancellationToken;
}
```

### XML Documentation Comments

Use XML comments for public APIs and complex methods:

```csharp
/// <summary>
/// Calculates damage based on fall distance using Unity's physics system.
/// </summary>
/// <param name="fallDistance">The distance fallen in meters.</param>
/// <param name="cancellationToken">Token to cancel the operation.</param>
/// <returns>The calculated damage amount.</returns>
public async UniTask<int> CalculateFallDamageAsync(float fallDistance, CancellationToken cancellationToken)
{
	// Implementation
}
```

---

## Version Control

### Git Workflow

We follow a branching strategy similar to Git Flow with specific adaptations for our workflow.

### Branch Structure

**Main Branches:**

- **`main`** (or `master`) - Source of truth for production/released code
- **`develop`** - Source of truth for work in progress and integration branch for features

**Supporting Branches:**

- **Feature branches** - Branch from `develop` for new features
- **Release branches** - Created from `develop` before merging to `main`

### Branch Naming Conventions

**All branch names must be lowercase.**

```bash
# Feature branches (use shorthand 'feat/')
feat/player-inventory
feat/dialogue-system
feat/jira-123-enemy-ai

# Release branches (semantic versioning)
release/1.0.0
release/1.2.1
release/2.0.0-beta
```

**When using project management tools (e.g., Jira):**
- Include ticket ID in branch name or PR title
- Preferred: `feat/jira-123-inventory-system`
- Alternative: Include ticket ID in PR title if not in branch name

### Feature Development Workflow

1. **Create feature branch from `develop`:**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feat/player-inventory
   ```

2. **Work on feature with regular commits**

3. **Before creating PR, bring feature inline with `develop`:**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout feat/player-inventory
   git merge develop
   ```
   
   **Why merge instead of rebase:**
   - Preserves true history and original commit hashes
   - Safe for branches shared with other developers
   - Avoids force-push requirements
   - Shows merge conflicts in context
   - Easier to recover from mistakes

4. **Create PR from feature branch to `develop`**

5. **Squash and merge PR into `develop`:**
   - Use "Squash and merge" option
   - Provides clean, linear history on `develop`
   - Single commit per feature makes history readable

### Release Workflow

1. **Create release branch from `develop`:**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b release/1.0.1
   ```

2. **Perform final testing and bug fixes on release branch**
   - Only bug fixes and release preparation on this branch
   - No new features

3. **Create PR from release branch to `main`**

4. **Merge release to `main` and tag:**
   ```bash
   git checkout main
   git merge release/1.0.1
   git tag -a v1.0.1 -m "Release version 1.0.1"
   git push origin main --tags
   ```

5. **Merge release changes back to `develop`:**
   ```bash
   git checkout develop
   git merge release/1.0.1
   git push origin develop
   ```

6. **Delete release branch:**
   ```bash
   git branch -d release/1.0.1
   git push origin --delete release/1.0.1
   ```

### Adding Features to Existing Release Branches

When a feature merged to `develop` is later requested for inclusion in an existing release branch (e.g., priority changed during release cycle):

1. **Ensure feature is merged to `develop` first:**
   ```bash
   # Feature should be squash-merged to develop following normal workflow
   # This creates a single commit representing the entire feature
   ```

2. **Cherry-pick the squashed commit to release branch:**
   ```bash
   git checkout develop
   git log --oneline  # Find feature commit: abc1234 "Add player statistics tracking"
   
   git checkout release/1.0.1
   git cherry-pick abc1234
   git push origin release/1.0.1
   ```

3. **If cherry-pick has conflicts:**
   - Resolve conflicts manually for release context
   - Test thoroughly to ensure feature works in release state
   - Commit the resolution:
   ```bash
   git cherry-pick --continue
   git push origin release/1.0.1
   ```

4. **For features requiring significant adaptation:**
   - Create a separate branch from release for the adapted feature:
   ```bash
   git checkout release/1.0.1
   git checkout -b feat/statistics-for-release
   # Make necessary adaptations
   # Create PR to release/1.0.1
   ```

5. **After release completes:**
   - Release branch merges to `main` (with tag) and back to `develop`
   - Feature now exists in all branches
   - Cherry-picked features are part of release history

**Benefit of squash and merge for this workflow:**

This scenario demonstrates a key advantage of squash-and-merge: **Each feature is a single, atomic commit that can be easily cherry-picked to other branches.** Without squash-and-merge, you'd need to identify and cherry-pick multiple commits (or cherry-pick a range), which is error-prone and may include unintended changes.

```bash
# With squash and merge (simple)
git cherry-pick abc1234  # One commit, entire feature

# Without squash and merge (complex)
git cherry-pick def5678 ghi9012 jkl3456 mno7890  # Which commits? Did I get them all?
```

### Hotfix Workflow

Hotfix strategy depends on whether the fix is for production or pre-production code, and how far ahead the develop branch has progressed.

#### Production Hotfixes

**When production bug applies to develop (develop hasn't diverged significantly):**

1. **Create hotfix branch from `main`:**
   ```bash
   git checkout main
   git pull origin main
   git checkout -b hotfix/1.0.2-critical-bug
   ```

2. **Fix and test on hotfix branch**

3. **Merge to `main` and tag:**
   ```bash
   git checkout main
   git merge hotfix/1.0.2-critical-bug
   git tag -a v1.0.2 -m "Hotfix: Critical bug fix"
   git push origin main --tags
   ```

4. **Merge to `develop`:**
   ```bash
   git checkout develop
   git merge hotfix/1.0.2-critical-bug
   git push origin develop
   ```

5. **Delete hotfix branch:**
   ```bash
   git branch -d hotfix/1.0.2-critical-bug
   git push origin --delete hotfix/1.0.2-critical-bug
   ```

**When production bug applies but develop has diverged significantly:**

1. **Follow steps 1-3 above (create, fix, merge to main, tag)**

2. **Cherry-pick the fix to `develop`:**
   ```bash
   git checkout develop
   git cherry-pick <commit-hash>
   git push origin develop
   ```

3. **If cherry-pick conflicts occur:**
   - Resolve conflicts manually
   - Or create a separate fix branch from `develop` that addresses the same issue in the new context
   ```bash
   git checkout develop
   git checkout -b fix/critical-bug-for-develop
   # Manually apply fix appropriate for develop's context
   # Create PR to develop
   ```

#### Pre-Production Fixes

**For bugs only in `develop` (not yet in production):**

- Create `fix/` branch from `develop`
- Follow normal feature workflow
- No need for hotfix process

```bash
git checkout develop
git pull origin develop
git checkout -b fix/memory-leak
# Fix and test
# Create PR to develop
```

#### Decision Matrix

| Scenario | Branch From | Branch Type | Merge To | Then Merge To |
|----------|-------------|-------------|----------|---------------|
| Production bug, develop similar | `main` | `hotfix/` | `main` + tag | `develop` (merge) |
| Production bug, develop diverged | `main` | `hotfix/` | `main` + tag | `develop` (cherry-pick) |
| Pre-production bug only | `develop` | `fix/` | `develop` only | N/A |

**Rationale:**
- Cherry-picking gives control when develop has significant changes
- Avoids merge conflicts and unintended side effects
- Preserves the fix without bringing in hotfix's historical context
- If develop has diverged too far, the fix may need to be rewritten for develop's new context

### Differences from Git Flow

⚠️ **Key variations from standard Git Flow:**

| Aspect | Git Flow | This Standard |
|--------|----------|-------------------|
| **Feature PR merging** | Regular merge | **Squash and merge** |
| **Feature branch sync** | Merge from develop | **Same** (merge from develop) |
| **Hotfix branches** | Always from main to main+develop | **Context-dependent** (merge or cherry-pick based on divergence) |
| **Branch naming** | `feature/`, `hotfix/`, `release/` | **Shorthand:** `feat/`, `hotfix/`, `release/`, `fix/` |
| **Release branch merging** | Merged to both main and develop | **Same** (but may squash on develop) |

**Rationale for squash and merge:**
- **Clean, readable history** - `develop` shows features, not implementation details
- **Semantic commits** - Each commit represents a complete, meaningful unit of work
- **Reduced noise** - Eliminates "WIP", "fix typo", "address PR feedback" commits from develop
- **Simpler reverts** - Entire features can be reverted atomically with a single revert
- **Lower cognitive load** - Easier to understand what's in develop at a glance
- **Well-suited for short-lived features** - When features are cohesive units, detailed commit history is less valuable

**Exception for large features:**

For particularly large or complex features with multiple sub-components, consider using a **regular merge** instead of squash and merge to preserve:
- Detailed commit history showing incremental development
- Individual attribution for multiple contributors
- Ability to partially revert sub-features if needed
- Better `git blame` and bisect capabilities for debugging

```bash
# Large feature with meaningful sub-commits
git checkout develop
git merge --no-ff feat/large-multiplayer-system
# Preserves commits like:
#   - Add lobby system
#   - Add matchmaking service
#   - Add player synchronization
#   - Add voice chat integration
```

Use judgment: squash for typical features, regular merge for large multi-part features where sub-feature detail adds value.

### Commit Messages

Write clear, descriptive commit messages:

```bash
# Good - descriptive and concise
git commit -m "Add player inventory system with drag-and-drop support"
git commit -m "Fix memory leak in enemy spawner (JIRA-123)"
git commit -m "Refactor health system to use events instead of polling"

# Avoid - vague or uninformative
git commit -m "Fixed stuff"
git commit -m "WIP"
git commit -m "Updates"
```

**Format:**
- Use imperative mood ("Add feature" not "Added feature")
- Keep first line under 72 characters
- Include ticket ID when applicable
- Provide context in commit body for complex changes

### Best Practices

- **Pull before push** - Always pull latest changes before pushing
- **Small, focused commits** - Commit logical units of work
- **Test before PR** - Ensure code compiles and tests pass
- **Review your own PR** - Check the diff before requesting review
- **Keep feature branches short-lived** - Merge within days, not weeks
- **Delete merged branches** - Clean up branches after merging
- **Protect important branches** - Use branch protection rules on `main` and `develop`

---

## AI-Assisted Development (Vibe Coding)

### Philosophy

AI tools (LLMs, code assistants) are valuable productivity multipliers when used responsibly. These guidelines help maintain code quality while leveraging AI capabilities.

### Core Principles

**Developer Responsibility**

AI is a tool, not a replacement for understanding:
- **Review and understand** all AI-generated code before committing
- **Never blindly copy-paste** without comprehension
- **You own the code**, not the AI
- If you don't understand generated code, ask the AI to explain or simplify it

**Code Quality Standards Apply**

AI-generated code must meet all coding standards:
- Follow naming conventions from this document
- Match project architecture patterns
- Include proper error handling and cancellation token support
- Maintain consistency with existing codebase
- Adhere to SOLID principles and never nester philosophy

**Security & Privacy**

Protect proprietary and sensitive information:
- **Never share proprietary code** with cloud-based AI without permission
- **Be cautious with secrets** - API keys, passwords, business logic
- **Use local models** when handling sensitive code
- **Review AI tools' terms of service** and data policies
- Consider using AI tools with code privacy guarantees

### Recommended Practices

**Provide Context**

Help AI understand your project:
- Create and maintain `CLAUDE.md` or similar context files in repository root
- Include coding standards excerpts in prompts when relevant
- Reference existing patterns and architecture
- Point AI to similar existing code as examples

**Testing AI-Generated Code**

Always verify AI output:
- **Test thoroughly** - Don't assume AI output is correct
- **Verify edge cases** and error handling
- **Check Unity-specific behavior** - Lifecycle methods, serialization, etc.
- **Profile performance** for performance-sensitive code
- Run lint and type checking tools

**When AI Shines**

AI is particularly effective for:
- Generating boilerplate and repetitive patterns
- Writing initial documentation and XML comments
- Explaining unfamiliar or complex code
- Generating unit tests (review and enhance after)
- Refactoring code to match standards
- Code reviews (ask AI to review your code)
- Implementing well-defined algorithms
- Converting between formats (JSON to C# classes, etc.)

**When to Be Cautious**

Exercise extra care with AI for:
- Complex algorithms requiring deep understanding
- Security-critical code (authentication, encryption, etc.)
- Performance-sensitive code (hot paths, per-frame updates)
- Unity-specific lifecycle nuances
- Memory management and object pooling
- Threading and async/await patterns
- Platform-specific code

### Tool Selection

**Tool Agnostic Approach**

Use whatever AI tool works best for your workflow:
- **No mandated tools** - Choose what's effective for you
- **Current preference: Claude Code** - Particularly effective for Unity C# development
- Other options: GitHub Copilot, Cursor, Gemini CLI, Bezi, OpenAI Codex, etc.
- Experiment and use what increases your productivity

**AI Configuration Files**

Keep AI-specific configuration local:
- **Add AI config files to `.gitignore`** - Prevents repository clutter
- Examples to ignore:
  ```gitignore
  # AI Assistant Configuration
  .claude/
  .cursor/
  .copilot/
  .aider/
  **/CLAUDE_TEMP.md
  **/.ai-context/
  ```

**Project Context Files**

Maintain shared context files (DO commit these):
- `CLAUDE.md` - Project overview, architecture, standards
- `CONTRIBUTING.md` - Development setup and guidelines
- Feature-specific `README.md` files

### Practical Workflow

**Effective AI Prompting**

```markdown
Good prompt:
"Refactor this Unity MonoBehaviour to use async/await with UniTask instead of 
coroutines. Ensure cancellation tokens are linked to destroyCancellationToken. 
Follow the never nester principle with early returns."

Poor prompt:
"Make this code better"
```

**Code Review with AI**

Use AI as an additional reviewer:
- Ask AI to review code for potential issues
- Check for edge cases you might have missed
- Verify adherence to coding standards
- Identify potential performance problems

**Iterative Refinement**

Work with AI iteratively:
1. Generate initial implementation
2. Review and test
3. Ask AI to refine based on findings
4. Integrate with existing code
5. Final manual review and adjustments

### Anti-Patterns to Avoid

- ❌ Committing AI-generated code without reading it
- ❌ Accepting AI suggestions that violate project standards
- ❌ Using AI to write code you don't understand
- ❌ Sharing proprietary business logic with cloud AI tools
- ❌ Assuming AI-generated tests are comprehensive
- ❌ Letting AI dictate architecture decisions
- ❌ Trusting AI for security-critical implementations without expert review

### Best Practices Summary

✅ **DO:**
- Use AI to accelerate development
- Review and understand all generated code
- Test AI-generated code thoroughly
- Provide AI with project context
- Use AI for code reviews and documentation
- Keep AI configs in `.gitignore`

✅ **DON'T:**
- Commit code you don't understand
- Share sensitive code without permission
- Skip testing because "AI wrote it"
- Let AI override your judgment
- Force specific AI tools on team members

**Remember:** AI is a powerful assistant, but you are the developer. Your understanding, judgment, and responsibility for the code remain paramount.

---

## Summary

These standards prioritize:
- **Clarity and readability** over cleverness
- **Consistency** in style and structure
- **Maintainability** through SOLID principles
- **Modern async patterns** with proper lifecycle management
- **Self-documenting code** with minimal but meaningful comments

When Microsoft standards conflict with team preferences, this document's guidelines take precedence, with conflicts clearly marked with ⚠️.

---

## License

MIT License

Copyright (c) 2025 Paul Stamp

Originally developed for Nonatomic

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.