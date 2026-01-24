# Scaffold Structure

Complete file contents for a new project scaffold.

## package.json

```json
{
  "name": "project-name",
  "version": "1.0.0",
  "description": "",
  "type": "module",
  "scripts": {
    "compile": "tsc",
    "watch": "tsc -w",
    "serve": "NODE_ENV=development node --watch dist/index.js",
    "dev": "run-p -sr watch serve",
    "start": "run-s compile dev"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/cors": "^2.8.17",
    "@types/express": "^4.17.21",
    "npm-run-all": "^4.1.5",
    "typescript": "^5.5.4"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "dotenv": "^16.4.5",
    "express": "^4.19.2",
    "serverless-http": "^3.2.0",
    "zod": "^3.23.8"
  }
}
```

## tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## .nvmrc

```
v20
```

## .gitignore

```
node_modules
dist
.env
```

## .env.example

```
NODE_ENV=development
PORT=3000
ALLOWED_ORIGINS=http://localhost:3000
```

## src/index.ts

```typescript
import express from 'express'
import cors from 'cors'
import serverless from 'serverless-http'

import { config } from './config/config.js'

class Server {
  public app: express.Application
  public PORT: number

  constructor() {
    this.app = express()
    this.PORT = config.APP.PORT
    this.config()
    this.routes()
  }

  private config() {
    this.app.set('PORT', this.PORT)
    this.app.use(express.json())
    this.app.use(cors({ origin: config.APP.ALLOWED_ORIGINS }))
  }

  private routes() {
    // Add your routes here
    // Example: this.app.use('/users', userRouter)

    // Health check
    this.app.get('/health', (_, res) => res.status(200).json({ status: 'ok' }))

    // 404 handler
    this.app.use('/*', (_, res) => res.status(404).json({ error: 'Not found' }))
  }

  public listen() {
    this.app.listen(this.app.get('PORT'), () => {
      console.log(`Server running on http://localhost:${this.PORT}`)
    })
  }
}

const server = new Server()

// Local development
if (config.APP.IS_DEV) {
  server.listen()
}

// AWS Lambda handler
export const handler = serverless(server.app)
```

## src/config/config.ts

```typescript
import 'dotenv/config'

const config = {
  APP: {
    IS_DEV: process.env.NODE_ENV === 'development',
    PORT: process.env.PORT ? Number(process.env.PORT) : 3000,
    ALLOWED_ORIGINS: (process.env.ALLOWED_ORIGINS ?? '').split(',').filter(Boolean)
  }
}

export { config }
```

## src/config/app.interface.ts

```typescript
import { Request, Response, NextFunction } from 'express'

export type ExpressMiddleware = (
  req: Request,
  res: Response,
  next: NextFunction
) => Promise<void | Response> | void | Response
```

## src/shared/errors/http.error.ts

```typescript
export class HttpError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public details?: unknown
  ) {
    super(message)
    this.name = 'HttpError'
  }
}

export class BadRequestError extends HttpError {
  constructor(message: string, details?: unknown) {
    super(400, message, details)
    this.name = 'BadRequestError'
  }
}

export class NotFoundError extends HttpError {
  constructor(message: string) {
    super(404, message)
    this.name = 'NotFoundError'
  }
}

export class InternalServerError extends HttpError {
  constructor(message: string = 'An unexpected error occurred') {
    super(500, message)
    this.name = 'InternalServerError'
  }
}
```

## src/shared/middleware/validate.middleware.ts

```typescript
import { Request, Response, NextFunction } from 'express'
import { AnyZodObject, ZodError } from 'zod'

export const validate = (schema: AnyZodObject) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      await schema.parseAsync({
        body: req.body,
        query: req.query,
        params: req.params
      })
      next()
    } catch (error) {
      if (error instanceof ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: error.errors.map((e) => ({
            field: e.path.join('.'),
            message: e.message
          }))
        })
      }
      return res.status(500).json({ error: 'Internal server error' })
    }
  }
}
```
