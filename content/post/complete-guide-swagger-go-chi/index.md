---
title: "Complete Guide: Adding Swagger to Go + Chi Project"
description: "A comprehensive guide on integrating Swagger documentation into your Go projects using Chi router, with best practices and step-by-step instructions"
date: 2025-06-11
image: image.png
categories:
    - Go
tags:
    - swagger
    - go
    - chi
    - api
    - backend
---

Are you building a REST API with Go and Chi router? One of the most crucial aspects of API development is proper documentation. In this comprehensive guide, I'll walk you through the process of integrating Swagger (OpenAPI) documentation into your Go project using Chi router. You'll learn not just the basics, but also best practices and advanced techniques to create professional API documentation.

## Why Swagger Documentation?

Before diving into the implementation, let's understand why Swagger documentation is essential:

- **Interactive Documentation**: Provides a user-friendly interface for API exploration
- **Automatic Client Generation**: Generate client libraries in multiple languages
- **Testing Interface**: Test API endpoints directly from the browser
- **Industry Standard**: Widely adopted OpenAPI specification
- **Always Up-to-Date**: Documentation generated from code annotations stays synchronized

## Required Dependencies

Let's start by setting up the necessary packages:

### 1. swaggo/swag
```bash
go install github.com/swaggo/swag/cmd/swag@latest
```

This core package provides:
- Swagger/OpenAPI 2.0 documentation generation from Go annotations
- CLI tooling for documentation management
- Automatic swagger.json and swagger.yaml file creation
- Support for complex types and nested structures

### 2. swaggo/http-swagger
```sh
go get -u github.com/swaggo/http-swagger
```

This package offers:
- Swagger UI handler specifically for Chi router
- Browser-based API testing interface
- Lightweight integration capabilities

### 3. swaggo/files
```bash
go get -u github.com/swaggo/files
```

A utility package that:
- Manages static files for Swagger UI
- Handles efficient file serving
- Integrates seamlessly with http-swagger

## Project Structure

For optimal organization, structure your project like this:

```
project-root/
├── docs/           # Auto-generated Swagger files
├── cmd/
│   └── main.go    # Application entry point
└── internal/      # Core application code
```

## Implementation Guide

### 1. Main API Documentation

Start by adding the main API documentation annotations in your `main.go`:

```go
// Package main Yadwy Backend API
// @title Your API Name
// @version 1.0
// @description Your API description
// @termsOfService http://swagger.io/terms/

// @contact.name API Support
// @contact.email support@yourapi.com

// @license.name Apache 2.0
// @license.url http://www.apache.org/licenses/LICENSE-2.0.html

// @host localhost:3000
// @BasePath /
// @schemes http https

// @securityDefinitions.apikey BearerAuth
// @in header
// @name Authorization
// @description Enter the token with the `Bearer: ` prefix

package main
```

### 2. Router Setup

Configure your Chi router to serve Swagger documentation:

```go
import (
    httpSwagger "github.com/swaggo/http-swagger"
    _ "your-project/docs" // Required for swagger docs
)

func SetupRouter() http.Handler {
    r := chi.NewRouter()
    
    // Swagger endpoint
    r.Get("/swagger/*", httpSwagger.Handler(
        httpSwagger.URL("/swagger/doc.json"),
    ))
    
    return r
}
```

### 3. Document Your Endpoints

Here's how to document your API endpoints effectively:

```go
// @Summary Create user
// @Description Create a new user in the system
// @Tags users
// @Accept json
// @Produce json
// @Param user body CreateUserRequest true "User information"
// @Success 201 {object} CreateUserResponse
// @Failure 400 {object} ErrorResponse
// @Router /users [post]
func (h *Handler) CreateUser(w http.ResponseWriter, r *http.Request) {
    // Implementation
}
```

### 4. Model Documentation

Document your data models with clear examples:

```go
// User represents the user model
type User struct {
    ID        int64     `json:"id" example:"1"`
    Email     string    `json:"email" example:"user@example.com"`
    Name      string    `json:"name" example:"John Doe"`
    CreatedAt time.Time `json:"created_at"`
}
```

## Best Practices

### 1. Organized Documentation
- Group related endpoints using tags
- Maintain consistent naming conventions
- Structure documentation hierarchically

### 2. Comprehensive Examples
- Provide realistic data examples
- Include both success and error scenarios
- Show different use cases

### 3. Error Documentation
- Document all possible error responses
- Include error codes and messages
- Explain error recovery steps

### 4. Security Documentation
- Clearly document authentication methods
- Specify required headers and tokens
- Include authorization scopes

## Maintenance Tips

1. **Keep Documentation Updated**
   - Run `swag init` after API changes
   - Review generated documentation
   - Update examples regularly

2. **Version Control**
   - Commit generated docs with code
   - Track API changes systematically
   - Maintain documentation versions

## Testing Your Documentation

1. Access your Swagger UI:
   - Navigate to `http://localhost:8080/swagger/`
   - Test endpoints interactively
   - Verify documentation accuracy

2. Validate Documentation:
   - Review generated swagger.json
   - Ensure all endpoints are documented
   - Check for missing information

## Benefits of This Approach

1. **Documentation Quality**
   - Always synchronized with code
   - Interactive and user-friendly
   - Professional presentation

2. **Development Efficiency**
   - Faster API development
   - Clear endpoint structure
   - Better team collaboration

3. **Client Integration**
   - Easy client code generation
   - Multiple language support
   - Reduced integration time

## Conclusion

Integrating Swagger documentation into your Go + Chi project is a valuable investment that pays off in better API understanding, faster development, and improved collaboration. By following this guide and best practices, you'll create professional, maintainable API documentation that enhances your project's value.

Remember to keep your documentation up-to-date and utilize the interactive features of Swagger UI for testing and validation. The effort you put into good documentation will be appreciated by both your team members and API consumers.

Happy coding!
