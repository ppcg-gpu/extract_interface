# Automatic bindings generator for C interface using libclang

This project is a bindings generator for the Integer Set Library (ISL). It automatically generates language bindings (Python, C++, etc.) for the ISL library by parsing its C interface using libclang. ISL is a library for manipulating sets and relations of integer points bounded by linear constraints.

## Purpose

ISL (Integer Set Library) is a powerful C library that provides data structures and operations for manipulating sets and relations of integer points bounded by affine constraints. While ISL is written in C, it's often desirable to use it from higher-level languages like Python or with a more convenient C++ interface. This tool automates the process of creating these bindings by:

1. Parsing the ISL C header files using libclang
2. Extracting the interface information including types, functions, and their annotations
3. Generating binding code for the target language based on the extracted interface

## Supported Languages

The tool currently supports generating bindings for:

- **Python**: Complete Python bindings
- **C++**: Several variants of C++ interfaces
  - `cpp`: Basic C++ wrapper
  - `cpp-checked`: C++ wrapper with argument checking
  - `cpp-checked-conversion`: C++ wrapper with argument checking and conversion support
  - `template-cpp`: Template-based C++ wrapper

## How It Works

The binding generator works as follows:

1. **Interface Extraction**: Using libclang, the tool parses ISL's C header files and extracts functions and types marked with special annotations (like `__isl_export`).

2. **Annotation Processing**: The tool processes special annotations in the ISL code:
   - `__isl_give` / `__isl_take` / `__isl_keep`: Ownership transfer semantics
   - `__isl_export`: Marks functions/types to be exposed in bindings
   - `__isl_constructor`: Marks constructor functions
   - `__isl_overload`: Marks overloadable functions
   - `__isl_subclass`: Indicates subclass relationships

3. **Code Generation**: Based on the extracted interface and annotations, the tool generates binding code for the target language.

## Usage

The extract_interface tool is typically used as part of the ISL build process to generate language bindings. It's built from the source files in the interface/ directory and invoked with the ISL header files to generate the bindings.

```bash
./extract_interface <input_header> -language=<lang> -I<include_path>
```

### Arguments

- `<input_header>`: The ISL header file that contains the interface declarations
- `-language=<lang>`: Target language for bindings (python, cpp, cpp-checked, cpp-checked-conversion, template-cpp)
- `-I <path>`: Include directory for header files

### Example

The command below processes the ISL C interface defined in `all.h` header file, extracts the interface information, and generates Python bindings based on the annotations in the code:

```bash
./extract_interface all.h -language=python -I include
```

The generated Python module provides a complete interface to the ISL library that enables Python code to manipulate sets and relations of integer points bounded by affine constraints.

## Project Structure

- `extract_interface.{cc,h}`: Main entry point and interface extraction logic
- `generator.{cc,h}`: Base class for code generators
- `python.{cc,h}`: Python binding generator
- `cpp.{cc,h}`: Common functionality for C++ generators
- `plain_cpp.{cc,h}`: Basic C++ binding generator
- `cpp_conversion.{cc,h}`: C++ generator with conversion support
- `template_cpp.{cc,h}`: Template-based C++ generator

## Dependencies

- **libclang**: For parsing C/C++ code
- **LLVM**: Used by libclang for various utilities

## Implementation Details

The binding generator uses various clang/LLVM libraries to parse and process the ISL header files:

- Uses the Clang AST (Abstract Syntax Tree) to extract declarations
- Implements compatibility code for various versions of Clang/LLVM
- Extracts function signatures, argument types, return types, and annotations
- Generates appropriate wrapper code based on these extracted details

## License

This project is distributed under the BSD 2-Clause License.

## Credits

ISL Interface Generator is developed by Sven Verdoolaege.
