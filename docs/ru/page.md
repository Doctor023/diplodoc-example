# Документация по petstore-api-draft.yaml

```
openapi: 3.0.0
info:
  version: 1.0.0
  title: API Зоомагазина
  description: |
    Этот API позволяет управлять животными в инвентаре зоомагазина.
    Вы можете создавать, получать и просматривать список животных с их деталями.
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.zoostore.com/v1
    description: Продукционная среда
  - url: https://staging.api.zoostore.com/v1
    description: Тестовая среда для проверки

paths:
  /pets:
    get:
      summary: Получить список всех животных
      description: |
        Возвращает постраничный список доступных животных.
        Результаты могут быть отфильтрованы с помощью параметров запроса.
      operationId: getPets
      tags:
        - Животные
      parameters:
        - name: limit
          in: query
          description: Максимальное количество возвращаемых элементов (макс. 100)
          required: false
          schema:
            type: integer
            maximum: 100
            format: int32
      responses:
        '200':
          description: Успешная операция
          headers:
            x-next:
              description: Ссылка на следующую страницу результатов
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pets'
        default:
          description: Неожиданная ошибка
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

    post:
      summary: Добавить животное
      description: Добавляет новое животное в инвентарь магазина
      operationId: createPet
      tags:
        - Животные
      requestBody:
        description: Объект животного для добавления
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Pet'
        required: true
      responses:
        '201':
          description: Животное успешно добавлено
        default:
          description: Неожиданная ошибка
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

  /pets/{petId}:
    get:
      summary: Получить животное по ID
      description: Возвращает детальную информацию о конкретном животном
      operationId: getPetById
      tags:
        - Животные
      parameters:
        - name: petId
          in: path
          required: true
          description: Уникальный идентификатор животного
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Успешная операция
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '400':
          description: Указан неверный ID
        '404':
          description: Животное не найдено
        default:
          description: Неожиданная ошибка
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

components:
  schemas:
    Pet:
      type: object
      description: Животное в инвентаре
      required:
        - id
        - name
      properties:
        id:
          type: integer
          format: int64
          description: Уникальный идентификатор животного
          example: 12345
        name:
          type: string
          description: Имя животного
          example: "Барсик"
        tag:
          type: string
          description: Категория или тип животного
          example: "кот"
        status:
          type: string
          description: Текущий статус доступности
          enum: [available, pending, sold]
          default: available

    Pets:
      type: array
      description: Список животных с поддержкой пагинации
      maxItems: 100
      items:
        $ref: '#/components/schemas/Pet'

    Error:
      type: object
      description: Стандартный ответ об ошибке
      required:
        - code
        - message
      properties:
        code:
          type: integer
          format: int32
          description: HTTP код статуса
          example: 404
        message:
          type: string
          description: Описание ошибки
          example: "Животное не найдено"
        details:
          type: string
          description: Дополнительные детали ошибки
          example: "Запрошенный ID животного не найден в базе данных"

  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
      description: API-ключ для аутентификации

security:
  - ApiKeyAuth: []
```
