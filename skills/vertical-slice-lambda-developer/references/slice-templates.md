# Slice Templates

Templates for each layer of a vertical slice. Replace `feature` with the actual feature name.

## Routes Template

`src/feature/feature.routes.ts`

```typescript
import { Router } from 'express'

import { validate } from '../shared/middleware/validate.middleware.js'
import {
  getAll,
  getById,
  create,
  update,
  remove
} from './feature.controller.js'
import {
  createFeatureSchema,
  updateFeatureSchema,
  getFeatureByIdSchema
} from './feature.schemas.js'

const featureRouter = Router()

featureRouter.get('/', getAll)
featureRouter.get('/:id', validate(getFeatureByIdSchema), getById)
featureRouter.post('/', validate(createFeatureSchema), create)
featureRouter.put('/:id', validate(updateFeatureSchema), update)
featureRouter.delete('/:id', validate(getFeatureByIdSchema), remove)

export { featureRouter }
```

## Controller Template

`src/feature/feature.controller.ts`

```typescript
import { Request, Response } from 'express'
import { FeatureService } from './feature.service.js'
import { HttpError } from '../shared/errors/http.error.js'

const service = new FeatureService()

const getAll = async (req: Request, res: Response) => {
  try {
    const result = await service.findAll()
    return res.status(200).json({ data: result })
  } catch (error) {
    if (error instanceof HttpError) {
      return res.status(error.statusCode).json({
        error: error.message,
        details: error.details
      })
    }
    return res.status(500).json({ error: 'Internal server error' })
  }
}

const getById = async (req: Request, res: Response) => {
  try {
    const { id } = req.params
    const result = await service.findById(id)
    return res.status(200).json({ data: result })
  } catch (error) {
    if (error instanceof HttpError) {
      return res.status(error.statusCode).json({
        error: error.message,
        details: error.details
      })
    }
    return res.status(500).json({ error: 'Internal server error' })
  }
}

const create = async (req: Request, res: Response) => {
  try {
    const data = req.body
    const result = await service.create(data)
    return res.status(201).json({ data: result })
  } catch (error) {
    if (error instanceof HttpError) {
      return res.status(error.statusCode).json({
        error: error.message,
        details: error.details
      })
    }
    return res.status(500).json({ error: 'Internal server error' })
  }
}

const update = async (req: Request, res: Response) => {
  try {
    const { id } = req.params
    const data = req.body
    const result = await service.update(id, data)
    return res.status(200).json({ data: result })
  } catch (error) {
    if (error instanceof HttpError) {
      return res.status(error.statusCode).json({
        error: error.message,
        details: error.details
      })
    }
    return res.status(500).json({ error: 'Internal server error' })
  }
}

const remove = async (req: Request, res: Response) => {
  try {
    const { id } = req.params
    await service.delete(id)
    return res.status(204).send()
  } catch (error) {
    if (error instanceof HttpError) {
      return res.status(error.statusCode).json({
        error: error.message,
        details: error.details
      })
    }
    return res.status(500).json({ error: 'Internal server error' })
  }
}

export { getAll, getById, create, update, remove }
```

## Service Template

`src/feature/feature.service.ts`

```typescript
import { FeatureRepository } from './feature.repository.js'
import { NotFoundError } from '../shared/errors/http.error.js'
import type { CreateFeatureInput, UpdateFeatureInput } from './feature.schemas.js'

export class FeatureService {
  private repository: FeatureRepository

  constructor() {
    this.repository = new FeatureRepository()
  }

  async findAll() {
    return await this.repository.findAll()
  }

  async findById(id: string) {
    const result = await this.repository.findById(id)

    if (!result) {
      throw new NotFoundError(`Feature with id ${id} not found`)
    }

    return result
  }

  async create(data: CreateFeatureInput) {
    // Add business logic validations here
    return await this.repository.create(data)
  }

  async update(id: string, data: UpdateFeatureInput) {
    // Verify exists
    await this.findById(id)

    // Add business logic validations here
    return await this.repository.update(id, data)
  }

  async delete(id: string) {
    // Verify exists
    await this.findById(id)

    return await this.repository.delete(id)
  }
}
```

## Repository Template

`src/feature/feature.repository.ts`

```typescript
import type { CreateFeatureInput, UpdateFeatureInput } from './feature.schemas.js'

// Define your data type
interface Feature {
  id: string
  // Add other fields
}

export class FeatureRepository {
  // Replace with your actual data source (DynamoDB, RDS, S3, etc.)

  async findAll(): Promise<Feature[]> {
    try {
      // TODO: Implement data access
      // Example: return await db.query('SELECT * FROM features')
      return []
    } catch (error) {
      throw new Error(`Failed to fetch features: ${error}`)
    }
  }

  async findById(id: string): Promise<Feature | null> {
    try {
      // TODO: Implement data access
      // Example: return await db.query('SELECT * FROM features WHERE id = ?', [id])
      return null
    } catch (error) {
      throw new Error(`Failed to fetch feature: ${error}`)
    }
  }

  async create(data: CreateFeatureInput): Promise<Feature> {
    try {
      // TODO: Implement data access
      // Example: return await db.query('INSERT INTO features ...', data)
      return { id: 'generated-id', ...data } as Feature
    } catch (error) {
      throw new Error(`Failed to create feature: ${error}`)
    }
  }

  async update(id: string, data: UpdateFeatureInput): Promise<Feature> {
    try {
      // TODO: Implement data access
      // Example: return await db.query('UPDATE features SET ... WHERE id = ?', [data, id])
      return { id, ...data } as Feature
    } catch (error) {
      throw new Error(`Failed to update feature: ${error}`)
    }
  }

  async delete(id: string): Promise<void> {
    try {
      // TODO: Implement data access
      // Example: await db.query('DELETE FROM features WHERE id = ?', [id])
    } catch (error) {
      throw new Error(`Failed to delete feature: ${error}`)
    }
  }
}
```

## Schemas Template (Zod Validation)

`src/feature/feature.schemas.ts`

```typescript
import { z } from 'zod'

// GET by ID validation
export const getFeatureByIdSchema = z.object({
  params: z.object({
    id: z.string().min(1, 'ID is required')
  })
})

// POST validation
export const createFeatureSchema = z.object({
  body: z.object({
    name: z.string().min(1, 'Name is required').max(100),
    description: z.string().optional(),
    // Add more fields as needed
  })
})

// PUT validation
export const updateFeatureSchema = z.object({
  params: z.object({
    id: z.string().min(1, 'ID is required')
  }),
  body: z.object({
    name: z.string().min(1).max(100).optional(),
    description: z.string().optional(),
    // Add more fields as needed
  })
})

// Type exports for use in service/repository
export type CreateFeatureInput = z.infer<typeof createFeatureSchema>['body']
export type UpdateFeatureInput = z.infer<typeof updateFeatureSchema>['body']
```

## Complete Slice File List

When creating a new feature slice, create these 5 files:

1. `src/feature/feature.routes.ts` - Router definition
2. `src/feature/feature.controller.ts` - Request handlers
3. `src/feature/feature.service.ts` - Business logic
4. `src/feature/feature.repository.ts` - Data access
5. `src/feature/feature.schemas.ts` - Zod validation schemas

Then register the router in `src/index.ts`:

```typescript
import { featureRouter } from './feature/feature.routes.js'

// In routes() method:
this.app.use('/features', featureRouter)
```
