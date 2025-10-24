# Go Project Setup Guide

## Project Architecture Overview

Your Go project will follow a clean architecture pattern with clear separation of concerns. Here's how it maps from your current Express.js structure:

- **Express routes** → Go HTTP handlers in `handlers/`
- **EJS templates** → Go templates in `templates/`
- **JSON file storage** → JSON persistence with proper data layer
- **Client-side JS** → Static assets in `static/`

## Directory Structure

```
user-management-go/
├── cmd/
│   └── server/
│       └── main.go                 # Application entry point
├── internal/                       # Private application code
│   ├── config/
│   │   └── config.go              # Configuration management
│   ├── handlers/
│   │   ├── user.go                # User-related HTTP handlers
│   │   └── middleware.go          # Custom middleware
│   ├── models/
│   │   └── user.go                # User data structures
│   ├── services/
│   │   └── user.go                # Business logic layer
│   └── storage/
│       ├── interfaces.go          # Storage interfaces
│       └── json/
│           └── user_storage.go    # JSON file storage implementation
├── web/
│   ├── templates/                 # HTML templates
│   │   ├── base.html             # Base layout template
│   │   ├── form.html             # User registration form
│   │   ├── users-list.html       # Users list page
│   │   └── partials/
│   │       └── form-input.html   # Reusable form input component
│   └── static/                    # Static assets (CSS, JS, images)
│       ├── css/
│       │   └── style.css
│       └── js/
│           ├── validation.js
│           └── user-actions.js
├── data/                          # Data storage directory
│   └── users.json                # JSON database file
├── go.mod                         # Go module file
├── go.sum                         # Go module checksums
├── .gitignore                     # Git ignore file
└── README.md                      # Project documentation
```

## Step-by-Step Setup

### 1. Initialize the Go Module

```bash
mkdir user-management-go
cd user-management-go
go mod init user-management-go
```

### 2. Install Required Dependencies

```bash
# Web framework (Gin is popular and Express-like)
go get github.com/gin-gonic/gin

# UUID generation for user IDs
go get github.com/google/uuid

# Environment variable management
go get github.com/joho/godotenv

# Input validation
go get github.com/go-playground/validator/v10
```

### 3. Create Directory Structure

```bash
mkdir -p cmd/server
mkdir -p internal/{config,handlers,models,services,storage/json}
mkdir -p web/{templates/partials,static/{css,js}}
mkdir -p data
```

## Core Architecture Components

### 1. **Entry Point** (`cmd/server/main.go`)
- Initializes the application
- Sets up routing
- Starts the HTTP server
- Handles graceful shutdown

### 2. **Configuration** (`internal/config/config.go`)
- Server port configuration
- Database file paths
- Environment-specific settings

### 3. **Models** (`internal/models/user.go`)
- User data structure with validation tags
- JSON serialization tags
- Input validation rules

### 4. **Storage Layer** (`internal/storage/`)
- Interface-based design for easy testing
- JSON file implementation
- Thread-safe operations
- Error handling for file I/O

### 5. **Services** (`internal/services/user.go`)
- Business logic layer
- User validation
- ID generation
- Coordinates between handlers and storage

### 6. **Handlers** (`internal/handlers/user.go`)
- HTTP request/response handling
- Template rendering
- JSON API endpoints
- Input validation and error responses

### 7. **Templates** (`web/templates/`)
- Go's `html/template` package
- Template inheritance with base layouts
- Reusable partials
- XSS protection built-in

## Key Go Concepts You'll Use

### Template System
Go's template system is different from EJS:
- Uses `{{ }}` for expressions
- Built-in XSS protection
- Template inheritance through `{{ template "name" . }}`
- Pipeline operations for data transformation

### Struct Tags
Go uses struct tags for validation and JSON marshaling:
```go
type User struct {
    ID        string `json:"id" validate:"required"`
    FirstName string `json:"firstName" validate:"required,min=2,max=50,alpha"`
    Email     string `json:"email" validate:"required,email"`
}
```

### Interface-Based Design
Storage operations use interfaces for testability:
```go
type UserStorage interface {
    GetAll() ([]User, error)
    Create(user User) error
    Delete(id string) error
}
```

### Error Handling
Go's explicit error handling:
- Every operation that can fail returns an error
- Check errors explicitly
- Use custom error types when needed

## Development Workflow

### 1. **Hot Reloading** (Development)
Install Air for automatic recompilation:
```bash
go install github.com/cosmtrek/air@latest
air init  # Creates .air.toml config file
air       # Starts development server with hot reload
```

### 2. **Project Structure Benefits**
- **`cmd/`**: Multiple entry points (server, CLI tools, etc.)
- **`internal/`**: Private code that can't be imported by other projects
- **`web/`**: All web-related assets in one place
- **Layered architecture**: Easy testing and maintenance

### 3. **Testing Structure**
```
├── internal/
│   ├── handlers/
│   │   ├── user.go
│   │   └── user_test.go
│   ├── services/
│   │   ├── user.go
│   │   └── user_test.go
│   └── storage/
│       └── json/
│           ├── user_storage.go
│           └── user_storage_test.go
```

## Migration Strategy from Express.js

### 1. **Routing Comparison**
```javascript
// Express.js
app.get('/users/list', (req, res) => { ... })
app.post('/form/new', (req, res) => { ... })
```

```go
// Go with Gin
r.GET("/users/list", handlers.GetUsersList)
r.POST("/form/new", handlers.CreateUser)
```

### 2. **Template Rendering**
```javascript
// Express.js with EJS
res.render('usersList', { users });
```

```go
// Go templates
c.HTML(http.StatusOK, "users-list.html", gin.H{
    "users": users,
})
```

### 3. **JSON Responses**
```javascript
// Express.js
res.json({ success: true, data: user })
```

```go
// Go with Gin
c.JSON(http.StatusOK, gin.H{
    "success": true,
    "data": user,
})
```

## Next Steps

1. **Start with the basic structure**: Create directories and initialize the module
2. **Install templ**: `go install github.com/a-h/templ/cmd/templ@latest`
3. **Implement models**: Define your User struct with validation
4. **Create storage layer**: JSON file operations
5. **Build basic components**: Start with simple templ components
6. **Create handlers**: HTTP request handling that renders components
7. **Set up hot reloading**: Configure Air to watch .templ files
8. **Add validation**: Both server-side and client-side
9. **Add testing**: Unit tests for each layer including component tests

## Essential templ Commands

```bash
# Generate Go code from .templ files
templ generate

# Format templ files
templ fmt .

# Watch and regenerate on changes (use with Air)
templ generate --watch

# Get LSP support for VS Code
# Install the "templ" extension in VS Code
```

This architecture provides a solid foundation that's maintainable, testable, and follows Go best practices while giving you a modern, component-based templating system that will feel familiar coming from React/JSX patterns.