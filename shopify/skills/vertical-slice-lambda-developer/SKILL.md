---
name: vertical-slice-lambda-developer
description: |
  Development of AWS Lambda functions using Vertical Slice Architecture with
  Node.js + TypeScript. Use when the user asks to create Lambda functions,
  API endpoints, or backend features following the vertical slice pattern.
  Triggers: "lambda", "vertical slice", "crear feature", "aws function",
  "scaffold lambda", "nuevo endpoint", "api endpoint", "serverless",
  "express lambda", "crear slice", "agregar feature"
---

# Vertical Slice Lambda Developer

## Usage Modes

This skill supports two modes:

### 1. Scaffold Mode
Create a complete new project from scratch with the vertical slice structure.
Use when: "create new project", "scaffold", "start from scratch", "new lambda project"

### 2. Feature Mode
Add a new slice/feature to an existing project.
Use when: "add feature", "create endpoint", "new slice", "add route"

## Project Structure (Scaffold)

When creating a new project, generate this structure:

```
project-name/
├── src/
│   ├── config/
│   │   ├── app.interface.ts
│   │   └── config.ts
│   ├── shared/
│   │   ├── errors/
│   │   │   └── http.error.ts
│   │   └── middleware/
│   │       └── validate.middleware.ts
│   └── index.ts
├── .env.example
├── .gitignore
├── .nvmrc
├── package.json
└── tsconfig.json
```

See [references/scaffold-structure.md](references/scaffold-structure.md) for complete file contents.

## Slice Structure (4 Layers)

Each feature/slice contains 4 files:

```
src/feature-name/
├── feature-name.routes.ts      # Express Router definition
├── feature-name.controller.ts  # Request/Response handling
├── feature-name.service.ts     # Business logic
└── feature-name.repository.ts  # Data access layer
```

See [references/slice-templates.md](references/slice-templates.md) for templates.

## Conventions

### Naming
- Folders: `kebab-case` (e.g., `user-management`, `order-items`)
- Files: `kebab-case.layer.ts` (e.g., `user.controller.ts`, `order.service.ts`)
- Functions: `camelCase` (e.g., `getUserById`, `createOrder`)
- Classes: `PascalCase` (e.g., `UserRepository`, `OrderService`)
- Constants: `UPPER_SNAKE_CASE` (e.g., `MAX_RETRIES`, `DEFAULT_LIMIT`)

### Imports
- ALWAYS use `.js` extension for local imports (ES Modules requirement)
- Example: `import { UserService } from './user.service.js'`

### Exports
- ALWAYS use named exports, NEVER default exports
- Example: `export { userRouter }` not `export default userRouter`

### Async/Await
- ALWAYS use async/await in controllers, services, and repositories
- NEVER use `.then()` chains

### Error Handling
- Use try/catch in all async functions
- Throw custom errors from services
- Handle errors in controllers with appropriate HTTP status codes

## Request Validation with Zod

Every endpoint that receives data MUST validate using Zod schemas:

```typescript
import { z } from 'zod'

// Define schema
export const createUserSchema = z.object({
  body: z.object({
    email: z.string().email(),
    name: z.string().min(2).max(100),
    age: z.number().int().positive().optional()
  })
})

// Type inference
export type CreateUserInput = z.infer<typeof createUserSchema>['body']
```

Use the validation middleware:
```typescript
import { validate } from '../shared/middleware/validate.middleware.js'
import { createUserSchema } from './user.schemas.js'

userRouter.post('/', validate(createUserSchema), createUser)
```

## REST Response Format

### Success Responses

```typescript
// 200 OK - Resource retrieved
res.status(200).json({ data: user })

// 201 Created - Resource created
res.status(201).json({ data: newUser })

// 204 No Content - Successful deletion
res.status(204).send()
```

### Error Responses

```typescript
// 400 Bad Request - Validation error
res.status(400).json({
  error: 'Validation failed',
  details: errors
})

// 404 Not Found
res.status(404).json({
  error: 'Resource not found',
  message: 'User with id 123 not found'
})

// 500 Internal Server Error
res.status(500).json({
  error: 'Internal server error',
  message: 'An unexpected error occurred'
})
```

## Layer Responsibilities

### Routes (`feature.routes.ts`)
- Define Express Router
- Apply middleware (validation, auth)
- Map HTTP methods to controller functions
- NO business logic here

### Controller (`feature.controller.ts`)
- Extract data from request (body, params, query)
- Call service methods
- Format and send HTTP responses
- Handle errors with appropriate status codes
- NO direct data access here

### Service (`feature.service.ts`)
- Implement business logic
- Orchestrate repository calls
- Validate business rules
- Throw custom errors for invalid states
- NO HTTP-specific code here

### Repository (`feature.repository.ts`)
- Direct data access (DB, external APIs, S3, etc.)
- CRUD operations
- Data transformation
- NO business logic here

## Workflow

1. Identify if it's a scaffold or feature request
2. For scaffold: Generate complete project structure
3. For feature: Generate the 4 slice files + schema file if needed
4. Follow naming conventions strictly
5. Include Zod validation for all input endpoints
6. Use REST standard responses

## Adding a New Feature Checklist

When adding a new slice to an existing project:

1. Create folder: `src/feature-name/`
2. Create files:
   - `feature-name.routes.ts`
   - `feature-name.controller.ts`
   - `feature-name.service.ts`
   - `feature-name.repository.ts`
   - `feature-name.schemas.ts` (if validation needed)
3. Register route in `src/index.ts`:
   ```typescript
   import { featureRouter } from './feature-name/feature-name.routes.js'
   this.app.use('/feature-name', featureRouter)
   ```
