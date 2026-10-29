# Autotests de API: Creación de usuario (`firstName`) Urban Routes

Proyecto de automatización de pruebas para el endpoint de **creación de usuarios**, desarrollado como parte de mi formación en QA Engineering. Las pruebas validan las reglas del parámetro `firstName` a partir de una lista de comprobación.

## Regla que se valida

El parámetro `firstName` (string):

- Solo puede contener **letras latinas**.
- Su longitud debe ser de **2 a 15 caracteres**.
- Es obligatorio.

## Tecnologías

- Python 3
- [pytest](https://docs.pytest.org/)
- [requests](https://requests.readthedocs.io/)
- PyCharm

## Estructura del proyecto

```
api_stand_tests/
├── configuration.py         # URL del servicio y rutas de los endpoints
├── data.py                  # Encabezados y cuerpo de solicitud base (user_body)
├── sender_stand_request.py  # Funciones que envían las solicitudes HTTP
└── create_user_test.py      # Autotests
```

### Endpoints utilizados

| Método | Ruta                              | Uso                                  |
|--------|-----------------------------------|--------------------------------------|
| POST   | `/api/v1/users/`                  | Crear un nuevo usuario               |
| GET    | `/api/db/resources/user_model.csv`| Consultar la tabla de usuarios       |

## Funciones auxiliares

| Función                              | Descripción                                                                                       |
|--------------------------------------|---------------------------------------------------------------------------------------------------|
| `get_user_body(first_name)`          | Copia el cuerpo base de `data.py` y cambia solo el valor de `firstName`.                          |
| `positive_assert(first_name)`        | Verifica el caso exitoso: código 201, `authToken` no vacío y usuario presente una sola vez en la tabla. |
| `negative_assert_symbol(first_name)` | Verifica el error por nombre inválido: código 400 y mensaje de error de validación.               |
| `negative_assert_no_firstname(user_body)` | Verifica el error por parámetro faltante o vacío: código 400 y mensaje de parámetros requeridos. |

## Casos de prueba

| N.º | Descripción                          | Valor de `firstName`  | Resultado esperado |
|-----|--------------------------------------|-----------------------|--------------------|
| 1   | Mínimo permitido (2 caracteres)      | `Aa`                  | 201 Created        |
| 2   | Máximo permitido (15 caracteres)     | `Aaaaaaaaaaaaaaa`     | 201 Created        |
| 3   | Menor a la cantidad permitida (1)    | `A`                   | 400 Bad Request    |
| 4   | Mayor a la cantidad permitida (16)   | `Aaaaaaaaaaaaaaaa`    | 400 Bad Request    |
| 5   | Contiene espacios                    | `A Aaa`               | 400 Bad Request    |
| 6   | Contiene caracteres especiales       | `"№%@",`              | 400 Bad Request    |
| 7   | Contiene números                     | `123`                 | 400 Bad Request    |
| 8   | No se envía el parámetro             | *(sin `firstName`)*   | 400 Bad Request    |
| 9   | Cadena vacía                         | `""`                  | 400 Bad Request    |
| 10  | Tipo de dato incorrecto (número)     | `12`                  | 400 Bad Request    |

### Verificaciones por tipo de prueba

**Positivas (casos 1 y 2)**

- El código de respuesta es `201`.
- El campo `authToken` existe y no está vacío.
- El usuario aparece **una sola vez** en la tabla `users` (sin duplicados).

**Negativas de nombre inválido (casos 3 a 7)**

- El código de respuesta es `400`.
- El campo `code` del cuerpo de la respuesta es `400`.
- El campo `message` es: `Has introducido un nombre de usuario no válido. El nombre solo puede contener letras del alfabeto latino, la longitud debe ser de 2 a 15 caracteres.`

**Negativas de parámetro faltante o vacío (casos 8 y 9)**

- El código de respuesta es `400`.
- El campo `code` del cuerpo de la respuesta es `400`.
- El campo `message` es: `No se han aprobado todos los parámetros requeridos`.

**Tipo de dato incorrecto (caso 10)**

- El código de respuesta es `400`.

## Cómo ejecutar las pruebas

1. Clonar el repositorio e ingresar a la carpeta del proyecto.
2. Crear y activar un entorno virtual (opcional, recomendado).
3. Instalar las dependencias:

   ```bash
   pip install pytest requests
   ```

4. Verificar que `URL_SERVICE` en `configuration.py` apunte al servidor activo (la URL del stand es temporal y puede cambiar).
5. Ejecutar todas las pruebas:

   ```bash
   python -m pytest create_user_test.py -v
   ```

## Defecto encontrado

**Caso 5: la API acepta espacios en `firstName`.**

- **Esperado (según la lista de comprobación):** `400 Bad Request` al enviar `"firstName": "A Aaa"`.
- **Actual:** la API acepta el valor y crea el usuario (`201 Created`).
- **Estado:** la prueba falla a propósito, porque el comportamiento de la API no cumple con la especificación. No se modificó el test para que pase.

## Autora

**Cristina Quintero**

- LinkedIn: [linkedin.com/in/cristina-quinteroh](https://www.linkedin.com/in/cristina-quinteroh)
- Email: [cristinaquintero459@gmail.com](mailto:cristinaquintero459@gmail.com)
