# webapp-core: A Robust Backend Model Layer Framework

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A sophisticated Rust framework implementing a Model Layer architecture with Backend Model Controllers (BMC), providing a clean and efficient approach to data access and business logic management.

## Key Features

- **BMC (Backend Model Controller) Pattern**: A refined approach to organizing model layer logic that separates concerns and promotes maintainability
- **Context-Aware Operations**: Built-in context mechanism for tracking user sessions and maintaining security
- **Efficient Store Management**: Advanced transaction handling and connection pooling
- **Type-Safe Query Building**: Leverages sea-query for type-safe SQL query construction
- **Code Generation Macros**: Reduces boilerplate while maintaining flexibility

## Architecture Overview

### The BMC Pattern

The Backend Model Controller (BMC) pattern is the core architectural concept, providing a structured way to implement data access and business logic:

```rust
pub trait DbBmc {
    const TABLE: &'static str;
    
    fn table_ref() -> TableRef {
        TableRef::Table(SIden(Self::TABLE).into_iden())
    }
    
    // Configurable features for entities
    fn has_timestamps() -> bool { true }
    fn has_owner_id() -> bool { false }
}
```

Each entity in the system has its own BMC implementation, providing:
- Type-safe database operations
- Business logic encapsulation
- Standardized CRUD operations
- Custom query capabilities

### Context Mechanism

The framework implements a robust context system that:
- Tracks user authentication state
- Manages user permissions
- Provides audit trail capabilities
- Enables security at the model layer

### Store Layer

The store layer provides sophisticated database interaction features:

- Connection pooling with configurable limits
- Transaction management with nested transaction support
- Error handling with detailed database-specific information
- Query execution with type-safe results

### Code Generation

The framework includes powerful macros for reducing boilerplate while maintaining flexibility:

```rust
generate_common_bmc_fns! {
    Bmc: UserBmc,
    Entity: User,
    ForCreate: UserForCreate,
    ForUpdate: UserForUpdate,
    Filter: UserFilter,
}
```

This generates standardized CRUD operations while allowing for custom overrides when needed.

## Example Usage

```rust
// Define your entity
#[derive(Clone, Fields, FromRow, Debug, Serialize)]
pub struct User {
    pub id: i64,
    pub username: String,
    pub typ: UserTyp,
}

// Implement the BMC
pub struct UserBmc;

impl DbBmc for UserBmc {
    const TABLE: &'static str = "user";
}

// Use the generated CRUD operations
async fn create_user(ctx: &Ctx, mm: &ModelManager, user: UserForCreate) -> Result<i64> {
    UserBmc::create(ctx, mm, user).await
}
```

## Key Components

- `ModelManager`: Central component managing database connections and transactions
- `Ctx`: Context structure carrying user session information
- `DbBmc`: Trait defining the contract for Backend Model Controllers
- `Error`: Comprehensive error handling system with detailed error types
- Store utilities for efficient database operations

## Installation

Add to your `Cargo.toml`:

```toml
[dependencies]
webapp-core = { path = "path/to/webapp-core" }
```

## Contributing

While this is primarily a portfolio piece, issues and pull requests are welcome. Please ensure you follow the existing code style and include tests for new features.

## Author

**eohyungk**

## License

This project is licensed under the MIT License - see the LICENSE file for details.
