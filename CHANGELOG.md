# Change Log

## Version 1.2.0

### Features
- Add comprehensive syntax highlighter for DSM language
- Add intelligent snippets for all DSM types and constructs
- Add problem matcher for `dsm_util.py` integration
- Add auto-closing pairs and auto-indentation
- Add code folding support with region markers

### Improvements
- Enhance README with advanced examples from real-world DSM models
- Add concept inheritance, attachments, and function pool examples
- Add real-world examples section referencing `dsm_samples/`
- Add comprehensive VS Code tasks integration guide with examples
- Document problem matcher usage with `dsm_util.py check`
- Show how to set up build tasks and auto-validation on save
- Clarify UUID placeholder generation in snippets
- Add detailed documentation for all features
- Fix email address typo in package metadata

### Snippets - Basic
- **Primitive types**: `bool`, `int8-64`, `uint8-64`, `float`, `double`, `string`, `uuid`, `blob_id`
- **Collections**: `vec`, `mat`, `tuple`, `optional`, `vector`, `set`, `map`, `xarray`, `variant`, `key`
- **Structures**: `namespace`, `concept`, `club`, `membership`, `enum`, `struct`, `attachment`
- **Function pools**: `function_pool`, `attachment_function_pool`
- **Utilities**: `docstr`, `aid`

### Snippets - Advanced (New)
- **`isa`**: Concept inheritance with `is a` keyword
- **`struct-defaults`**: Structure with default field values
- **`enum-field`**: Enum field with default value (`.enumValue` syntax)
- **`attachment-block`**: Complete attachment namespace with docstring
- **`keyfield`**: Key field following naming conventions (`fieldNameKey`)
- **`opt-key`**: Optional key field for nullable concept references
- **`mutable`**: Mutable function declaration for attachment function pools
- **`query`**: Query function declaration for attachment function pools

### Advanced Examples
- Concept inheritance patterns with real Material system examples
- Default values in structures (Transform, Camera)
- Enumerations with default values (CameraDepthRangePolicy)
- Attachments linking concepts to data (Graph topology)
- Function pools with complete documentation
- **Attachment function pools** with mutable operations and queries (NEW)
  - ModelGraph: topology editing functions
  - ModelSelection: selection management functions
  - ModelIntegrity: data integrity restoration functions
- Docstring best practices
- Mutable vs query functions in collaborative editing context 
