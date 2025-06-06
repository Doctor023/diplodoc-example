# Документация по petstore-api-draft.yaml

```
openapi: 3.0.0

info:
  version: 1.0.0
  title: Petstore API
  description: |
    This API allows you to manage pets in a pet store inventory.
    You can create, retrieve, and list pets with their details.
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.petstore.com/v1
    description: Production environment
  - url: https://staging.api.petstore.com/v1
    description: Staging environment for testing

paths:
  /pets:
    get:
      summary: List all pets
      description: |
        Retrieves a paginated list of available pets.
        Results can be filtered using query parameters.
      operationId: getPets
      tags:
        - Pets
      parameters:
        - name: limit
          in: query
          description: Maximum number of items to return (max 100)
          required: false
          schema:
            type: integer
            maximum: 100
            format: int32
      responses:
        '200':
          description: Successful operation
          headers:
            x-next:
              description: Link to the next page of results
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pets'
        default:
          description: Unexpected error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

    post:
      summary: Create a pet
      description: Adds a new pet to the store inventory
      operationId: createPet
      tags:
        - Pets
      requestBody:
        description: Pet object that needs to be added
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Pet'
        required: true
      responses:
        '201':
          description: Pet created successfully
        default:
          description: Unexpected error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

  /pets/{petId}:
    get:
      summary: Get pet by ID
      description: Returns detailed information about a specific pet
      operationId: getPetById
      tags:
        - Pets
      parameters:
        - name: petId
          in: path
          required: true
          description: Unique identifier of the pet
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Successful operation
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '400':
          description: Invalid ID supplied
        '404':
          description: Pet not found
        default:
          description: Unexpected error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

components:
  schemas:
    Pet:
      type: object
      description: A pet in the inventory
      required:
        - id
        - name
      properties:
        id:
          type: integer
          format: int64
          description: Unique identifier for the pet
          example: 12345
        name:
          type: string
          description: Name of the pet
          example: "Fluffy"
        tag:
          type: string
          description: Category or type of pet
          example: "dog"
        status:
          type: string
          description: Current availability status
          enum: [available, pending, sold]
          default: available

    Pets:
      type: array
      description: List of pets with pagination support
      maxItems: 100
      items:
        $ref: '#/components/schemas/Pet'

    Error:
      type: object
      description: Standard error response
      required:
        - code
        - message
      properties:
        code:
          type: integer
          format: int32
          description: HTTP status code
          example: 404
        message:
          type: string
          description: Error description
          example: "Pet not found"
        details:
          type: string
          description: Additional error details
          example: "The requested pet ID was not found in the database"

  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
      description: API key for authentication

security:
  - ApiKeyAuth: []
```
