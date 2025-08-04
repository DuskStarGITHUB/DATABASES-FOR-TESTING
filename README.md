
# 🗃️ Sistema de Registro de Ventas — Documentación de Base de Datos

Este proyecto contiene un sistema completo de registro de ventas con múltiples entidades relacionadas. A continuación, se documenta la estructura de la base de datos, el script de inserción y una consulta SQL útil para obtener una vista completa del sistema.

---

## 🧩 Estructura de la Base de Datos

### `accounts`
| Campo          | Tipo           | Descripción                        |
|----------------|----------------|------------------------------------|
| id             | int, PK        | ID único de la cuenta              |
| username       | varchar(50)    | Nombre de usuario único            |
| email          | varchar(100)   | Correo electrónico único           |
| password_hash  | varchar(255)   | Contraseña en hash                 |
| is_active      | tinyint(1)     | Activa o no                        |
| created_at     | timestamp      | Fecha de creación                  |

---

### `profiles`
| Campo       | Tipo         | Descripción                     |
|-------------|--------------|---------------------------------|
| id          | int, PK      | ID de perfil                    |
| account_id  | int, FK      | Relación con `accounts.id`      |
| full_name   | varchar(100) | Nombre completo                 |
| address     | text         | Dirección                       |
| phone       | varchar(20)  | Teléfono                        |
| birthdate   | date         | Fecha de nacimiento             |
| created_at  | timestamp    | Fecha de creación               |

---

### `suppliers`
| Campo         | Tipo          | Descripción               |
|---------------|---------------|---------------------------|
| id            | int, PK       | ID de proveedor           |
| name          | varchar(100)  | Nombre de empresa         |
| contact_name  | varchar(100)  | Persona de contacto       |
| email         | varchar(100)  | Correo                    |
| phone         | varchar(20)   | Teléfono                  |
| address       | text          | Dirección                 |
| created_at    | timestamp     | Fecha de registro         |

---

### `articles`
| Campo       | Tipo          | Descripción                         |
|-------------|---------------|-------------------------------------|
| id          | int, PK       | ID del artículo                     |
| supplier_id | int, FK       | Relación con `suppliers.id`         |
| name        | varchar(100)  | Nombre del artículo                 |
| description | text          | Descripción                         |
| price       | decimal(10,2) | Precio unitario                     |
| stock       | int           | Stock disponible                    |
| is_active   | tinyint(1)    | Activo o no                         |
| created_at  | timestamp     | Fecha de creación                   |

---

### `direct_clients`
| Campo      | Tipo          | Descripción          |
|------------|---------------|----------------------|
| id         | int, PK       | ID del cliente       |
| full_name  | varchar(100)  | Nombre completo      |
| email      | varchar(100)  | Correo               |
| phone      | varchar(20)   | Teléfono             |
| address    | text          | Dirección            |
| created_at | timestamp     | Fecha de registro    |

---

### `sales`
| Campo      | Tipo               | Descripción                          |
|------------|--------------------|--------------------------------------|
| id         | int, PK            | ID de la venta                       |
| client_id  | int, FK            | Relación con `direct_clients.id`     |
| sale_date  | datetime           | Fecha de la venta                    |
| status     | enum               | Estado: pending, shipped, etc.       |
| total      | decimal(10,2)      | Total de la venta                    |

---

### `sale_details`
| Campo       | Tipo           | Descripción                         |
|-------------|----------------|-------------------------------------|
| id          | int, PK        | ID del detalle de venta             |
| sale_id     | int, FK        | Relación con `sales.id`             |
| article_id  | int, FK        | Relación con `articles.id`          |
| quantity    | int            | Cantidad vendida                    |
| unit_price  | decimal(10,2)  | Precio unitario en el momento       |

---

### `registers_test`
| Campo            | Tipo            | Descripción                         |
|------------------|-----------------|-------------------------------------|
| id_register      | int, PK         | ID de registro                      |
| nombre           | varchar(50)     | Nombre                              |
| apellido_paterno | varchar(50)     | Apellido paterno                    |
| apellido_materno | varchar(50)     | Apellido materno                    |
| numero           | varchar(20)     | Teléfono                            |
| correo           | varchar(100)    | Correo                              |
| genero           | enum(M,F,O)     | Género                              |
| edad             | int             | Edad                                |
| dinero_en_cuenta | decimal(15,2)   | Saldo                               |
| fecha_registro   | timestamp       | Fecha de registro                   |

---

## 🧮 Consulta SQL para Ver Ventas con Detalles

Esta consulta une todas las tablas relacionadas para mostrar una vista completa de las ventas:

```sql
SELECT 
    c.id AS client_id,
    c.full_name AS client_name,
    c.email AS client_email,

    s.id AS sale_id,
    s.sale_date,
    s.status,
    s.total,

    sd.quantity,
    sd.unit_price,

    ar.id AS article_id,
    ar.name AS article_name,
    ar.price AS article_price,

    sp.id AS supplier_id,
    sp.name AS supplier_name

FROM sales s
LEFT JOIN direct_clients c ON s.client_id = c.id
LEFT JOIN sale_details sd ON sd.sale_id = s.id
LEFT JOIN articles ar ON ar.id = sd.article_id
LEFT JOIN suppliers sp ON ar.supplier_id = sp.id

ORDER BY s.id DESC
LIMIT 100;
```

---

## 🐍 Script Python de Inserción Automática

El archivo `insert_2.py` genera e inserta datos falsos realistas en todas las tablas automáticamente usando la librería [`Faker`](https://faker.readthedocs.io/), controlando colisiones de claves únicas y permitiendo detener el proceso con la tecla `s`.

```bash
source venv/bin/activate
python insert_2.py
```

Características:

- Inserta proveedores, artículos, cuentas, perfiles, clientes y ventas
- Evita errores de duplicados con `try/except`
- Corre indefinidamente hasta que presiones `s` y ENTER
- Ideal para pruebas de carga o desarrollo

---

## ✍️ Autor

> Generado con amor por Speencer DuskStar Dust (a.k.a. Dusk) ✨  
> Servidor: `eva01`  
> Fecha: 2025-08-04  
> Uso interno y educativo 🧪
