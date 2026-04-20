# DSM Syntax Highlighter

**Official VS Code extension for the Digital Substrate Model (DSM) language**

The DSM extension provides comprehensive language support for `.dsm` files, including syntax highlighting, intelligent snippets, and integration with DSM tools.

## Features

### Syntax Highlighting
- **Keywords**: `namespace`, `concept`, `club`, `membership`, `enum`, `struct`, `attachment`, `function_pool`
- **Types**: All primitive types (`bool`, `int8-64`, `uint8-64`, `float`, `double`, `string`, `uuid`, `blob_id`)
- **Collections**: `vec`, `mat`, `tuple`, `optional`, `vector`, `set`, `map`, `xarray`, `variant`
- **UUIDs**: Automatic recognition and highlighting of UUID patterns
- **Comments**: Line comments (`//`) and docstrings (`"""..."""`)
- **Enumerations**: Enum value syntax (`.EnumValue`)

### Smart Snippets
Type prefixes and press Tab to insert common DSM constructs:

**Primitive Types:**
- `bool`, `string`, `uuid`, `blob_id`
- `int8`, `int16`, `int32`, `int64`
- `uint8`, `uint16`, `uint32`, `uint64`
- `float`, `double`

**Collections:**
- `vec` → `vec<T, size>` (numeric vectors)
- `mat` → `mat<T, cols, rows>` (matrices)
- `optional` → `optional<T>`
- `vector` → `vector<T>`
- `set` → `set<T>`
- `map` → `map<K, V>`
- `tuple` → `tuple<T0, T1>`
- `variant` → `variant<T0, T1>`
- `xarray` → `xarray<T>`
- `key` → `key<T>`

**Structures:**
- `namespace` → Full namespace template with UUID placeholder
- `concept` → Concept declaration
- `club` → Club declaration
- `membership` → Membership declaration
- `enum` → Enumeration template
- `struct` → Structure template
- `attachment` → Attachment declaration
- `function_pool` → Function pool template with UUID
- `attachment_function_pool` → Attachment function pool template
- `docstr` → Documentation string (`"""..."""`)

**Advanced Patterns:**
- `isa` → Concept inheritance (`concept Derived is a Base;`)
- `struct-defaults` → Structure with default field values
- `enum-field` → Enum field with default value (`.enumValue`)
- `attachment-block` → Complete attachment namespace with docstring
- `keyfield` → Key field with naming convention (`key<Concept> fieldKey;`)
- `opt-key` → Optional key field (`optional<key<Concept>> fieldKey;`)
- `mutable` → Mutable function in attachment function pool (state-modifying)
- `query` → Query function in attachment function pool (read-only)

### Editor Configuration
- **Auto-closing**: Automatic closing of brackets `{}`, `[]`, `()`, `<>` and quotes
- **Auto-indentation**: Smart indentation after `struct` and `enum` declarations
- **Folding**: Support for code regions with `// #region` / `// #endregion`

### Problem Matcher
Integrated problem matcher (`$dsm`) for `dsm_util.py` output, automatically parsing errors into the VS Code Problems panel.

### VS Code Tasks Integration
Create custom build tasks to validate DSM files on demand or automatically.

## Usage Examples

### Creating a Simple Concept
```dsm
// Type 'concept' and press Tab
concept Person;

struct PersonData {
    string name;
    uint32 age;
    optional<string> email;
};
```

### Using Collections
```dsm
// Type 'vec' and press Tab, then select type and size
vec<float, 3> position;

// Type 'map' and press Tab
map<string, PersonData> people;
```

### Working with Namespaces
```dsm
// Type 'namespace' and press Tab
namespace MyApp {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx} {
    concept User;
    concept Session;
};
```

**Note:** Replace the UUID placeholder with an actual UUID. You can generate one using:
- Online tools (e.g., `uuidgen` command on macOS/Linux)
- Python: `python3 -c "import uuid; print(uuid.uuid4())"`
- VS Code extension: UUID Generator

## Advanced Examples

### Concept Inheritance
DSM supports single inheritance using the `is a` keyword:

```dsm
// Base concept
concept Material;

// Derived concepts
concept MaterialMatte is a Material;
concept MaterialMirror is a Material;
concept MaterialMultilayer is a Material;

// Each material type can have its own properties
struct MaterialMatteProperties {
    string name = "Matte Material";
    float threshold;
    float contrast = 1.0;
};
```

### Default Values in Structures
You can specify default values for struct fields:

```dsm
struct Transform {
    Vector translation;
    Vector orientation;
    Vector scaling = {1.0, 1.0, 1.0};  // Default value
};

struct CameraProperties {
    string name = "Camera";              // String default
    bool exposedInConfiguration = true;  // Boolean default
    float fov = 0.785398;               // Float default
    PointOfView pointOfView;            // Required field
};
```

### Enumerations with Default Values
Enums can be used as types with default values:

```dsm
enum CameraDepthRangePolicy {
    frustumBased,
    fixedDepthRange,
    lookAtPointBasedInterest,
    useGlobalPolicy
};

struct CameraDepthRange {
    CameraDepthRangePolicy policy = .lookAtPointBasedInterest;
    float zNear = 0.1;
    float zFar = 100.0;
};
```

### Attachments: Linking Concepts and Data
Attachments associate data structures with concepts:

```dsm
namespace Graph {27c49329-a399-415c-baf0-db42949d2ba2} {
    // Define concepts
    concept Graph;
    concept Vertex;
    concept Edge;

    // Define data structures
    struct GraphTopology {
        set<key<Vertex>> vertexKeys;
        set<key<Edge>> edgeKeys;
    };

    struct VertexVisualAttributes {
        int64 value;
        Color color;
    };

    // Attach data to concepts
    attachment<Graph, GraphTopology> topology;
    attachment<Vertex, VertexVisualAttributes> visualAttributes;
};
```

### Function Pools
Define callable functions with docstrings:

```dsm
"""This pool provides access to utility functions."""
function_pool Tools {dc9740c9-9d1d-4c1e-9caa-4c8843b91e82} {

    """Return a + b."""
    int64 add(int64 a, int64 b);

    """Return true if a is even."""
    bool isEven(int64 a);

    """Return a random word."""
    string randomWord(uint64 size);
};
```

### Attachment Function Pools
`attachment_function_pool` is used for operations that modify persistent data in a collaborative editing context. Functions can be **mutable** (modify state) or **queries** (read-only).

```dsm
"""This pool provides functions to edit the graph topology."""
attachment_function_pool ModelGraph {9bdcbb5b-76e9-426f-b8a6-a10ed2d949e6} {

    // Mutable functions modify state
    """Create a new edge between two vertices."""
    mutable key<Edge> newEdge(key<Graph> graphKey, key<Vertex> vaKey, key<Vertex> vbKey);

    """Create a vertex with specified attributes."""
    mutable key<Vertex> newVertex(key<Graph> graphKey, int64 value, Position position);

    """Set the vertex position."""
    mutable void setVertexPosition(key<Vertex> vertexKey, Position position);

    """Delete the selected elements."""
    mutable void deleteSelection(key<Graph> graphKey);
};
```

**Key differences from `function_pool`:**
- **`mutable` keyword**: Marks functions that modify state (create, update, delete)
- **Commit tracking**: All mutations are tracked for collaborative editing and undo/redo
- **Return keys**: Mutable functions can return `key<T>` for newly created entities
- **Query functions**: Non-mutable functions can query state without the `mutable` keyword

**Common patterns:**

```dsm
// Creating entities - returns key
mutable key<Vertex> newVertex(key<Graph> graphKey, int64 value, Position position);

// Modifying entities - void return
mutable void setVertexColor(key<Vertex> vertexKey, Color color);

// Querying state - no mutable keyword
set<key<Vertex>> selectedVertices(key<Graph> graphKey);

// Batch operations - operate on sets
mutable void moveVertices(set<key<Vertex>> vertexKeys, Position offset);
```

**Organizing attachment function pools by concern:**
```dsm
// Topology editing
attachment_function_pool ModelGraph {...} {
    mutable key<Vertex> newVertex(...);
    mutable key<Edge> newEdge(...);
    mutable void deleteSelection(...);
};

// Selection management
attachment_function_pool ModelSelection {...} {
    mutable void selectAll(key<Graph> graphKey);
    mutable void deselectAll(key<Graph> graphKey);
    set<key<Vertex>> selectedVertices(key<Graph> graphKey);
};

// Data integrity
attachment_function_pool ModelIntegrity {...} {
    mutable void restoreIntegrityByCreating(key<Graph> graphKey);
    mutable void restoreIntegrityByDeleting(key<Graph> graphKey);
};
```

### Documentation with Docstrings
Use triple-quoted strings for documentation:

```dsm
"""A graph data structure."""
concept Graph;

"""The vertices and edges of the graph topology."""
struct GraphTopology {
    """The vertex keys in the graph."""
    set<key<Vertex>> vertexKeys;

    """The edge keys in the graph."""
    set<key<Edge>> edgeKeys;
};
```

## Real-World Examples

Comprehensive DSM examples are available in the [dsm-samples](https://github.com/digital-substrate/dsm-samples) repository:

- **Graph modeling** (`Ge/`): Complete graph topology system demonstrating:
  - Concept definitions: `Graph`, `Vertex`, `Edge`
  - Attachment system for graph topology and visual attributes
  - **Attachment function pools** for collaborative editing:
    - `ModelGraph`: Topology editing (newVertex, newEdge, deleteSelection)
    - `ModelSelection`: Selection management (selectAll, deselectAll)
    - `ModelIntegrity`: Data integrity restoration
  - Mutable operations with proper return types (`key<T>`, `void`)
  - Query functions for state inspection

- **3D rendering system** (`Re/`): Raptor rendering engine models including:
  - Camera system with depth of field and motion blur
  - Material system with inheritance (matte, mirror, multilayer)
  - Lighting, textures, and environment
  - Sensors and timeline management

These examples demonstrate production-quality DSM modeling patterns and best practices.

## VS Code Tasks Setup

The DSM extension includes a problem matcher (`$dsm`) that integrates with VS Code tasks. You can create build tasks to validate DSM files automatically.

### Setting Up a DSM Validation Task

1. **Create a tasks configuration file** in your project:
   - Create `.vscode/tasks.json` in your project root
   - Or use VS Code: `Terminal` → `Configure Tasks...` → `Create tasks.json from template`

2. **Add the DSM syntax check task:**

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Check DSM Syntax",
            "type": "shell",
            "command": "python3",
            "args": [
                "${workspaceFolder}/tools/dsm_util.py",
                "check",
                "${file}"
            ],
            "presentation": {
                "reveal": "never",
                "revealProblems": "onProblem"
            },
            "problemMatcher": "$dsm",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

3. **Customize paths** based on your project structure:
   - Adjust `tools/dsm_util.py` path to match your setup
   - Use `${workspaceFolder}` for workspace-relative paths
   - Use `${file}` to check the currently open file

### Running the Task

- **Keyboard shortcut**: Press `Cmd+Shift+B` (macOS) or `Ctrl+Shift+B` (Windows/Linux)
- **Command Palette**: `Tasks: Run Build Task`
- **Menu**: `Terminal` → `Run Build Task...`

### How It Works

1. The task runs `dsm_util.py check` on your DSM file
2. The `$dsm` problem matcher parses the output
3. Errors appear in the **Problems panel** (`Cmd+Shift+M`)
4. Click on errors to jump directly to the line in your file

### Task Configuration Options

**`presentation.reveal`:**
- `"never"` - Don't show terminal output (cleanest)
- `"always"` - Always show terminal
- `"onProblem"` - Show only when errors occur

**`presentation.revealProblems`:**
- `"onProblem"` - Auto-open Problems panel on errors
- `"never"` - Don't auto-open Problems panel

**`group.isDefault`:**
- `true` - Makes this the default build task (runs with `Cmd+Shift+B`)
- `false` - Requires manual selection from task list

### Example: Auto-check on Save

Add a `file watcher` task to automatically validate on save:

```json
{
    "label": "Watch DSM Files",
    "type": "shell",
    "command": "python3",
    "args": [
        "${workspaceFolder}/tools/dsm_util.py",
        "check",
        "${file}"
    ],
    "problemMatcher": "$dsm",
    "runOptions": {
        "runOn": "folderOpen"
    }
}
```

Then configure VS Code to run tasks on save in `settings.json`:
```json
{
    "files.autoSave": "afterDelay",
    "task.autoDetect": "on"
}
```

**Note:** The actual path to `dsm_util.py` depends on your project structure. Common locations:
- `../tools/dsm_util.py` (from subdirectory)
- `tools/dsm_util.py` (from root)
- `python3 tools/dsm_util.py` (explicit Python interpreter)

## Tips

1. **Use Tab completion**: Start typing a keyword and press Tab to expand snippets
2. **Navigate by symbols**: Use `Ctrl+Shift+O` (Windows/Linux) or `Cmd+Shift+O` (macOS) to navigate between concepts, structs, and enums
3. **Fold regions**: Use `// #region Description` and `// #endregion` to organize large DSM files
4. **Validate with build task**: Set up a VS Code task (see above) and press `Cmd+Shift+B` to check syntax instantly
5. **Jump to errors**: Use `Cmd+Shift+M` to open the Problems panel and click errors to jump to their location
6. **Format with tools**: Use `python3 tools/dsm_util.py check <file.dsm>` to validate syntax from command line

## Requirements

- VS Code version 1.87.0 or higher
- DSM tools from the Viper project (for validation and code generation)

## Known Issues

None at this time. Please report issues to: support@digitalsubstrate.io

## Release Notes

See [CHANGELOG.md](CHANGELOG.md) for version history.

## License

MIT License — See [LICENSE](LICENSE) for details.

---

**Digital Substrate** | [Report an issue](https://github.com/digital-substrate/dsm-vscode/issues) | [Contributing](CONTRIBUTING.md)
