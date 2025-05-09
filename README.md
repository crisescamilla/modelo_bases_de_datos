# 📚 Sistema de Gestión de Librería

Este proyecto representa un **Modelo Entidad-Relación (MER)** para la gestión de una librería, incluyendo el manejo de clientes, pedidos, libros, editoriales e inventario.

## 🧩 Entidades Principales

### Cliente
- Identificado por: `Código_cliente`
- Atributos:
  - `ID`: Puede ser Persona Natural (PN) o Persona Jurídica (PJ)
    - PN: `DNI`, `RUT`
    - PJ: `NIT`, `RUES`
  - `Nombre`
  - `Dirección`: compuesta por `Calle`, `Barrio`, `Ciudad`, `Estado`
  - `Teléfono`
  - `Email`

### Pedido
- Identificado por: `Código_pedido`
- Atributos:
  - `Fecha`
  - `Valor`
- Relación:
  - Un cliente puede realizar muchos pedidos `(1,N)`.

### Libros
- Identificado por: `Código_libro`
- Atributos:
  - `Título`
  - `Categoría`
  - `ISBN`
  - `Año_publicación`
  - `Editorial` (nombre)
  - `Valor`
  - `Autor/a`
  - `Código_editorial`

### Editorial
- Identificada por: `Código_editorial`
- Atributos:
  - `Teléfono_1`
  - `Teléfono_2` (opcional)
  - `Nombre_contacto`
  - `Email`

### Inventario
- Representa los libros existentes por editorial
- Atributos:
  - `Cantidad`
- Clave foránea:
  - `Código_libro`
  - `Código_editorial`

## 🔗 Relaciones

- **Compra** `(1,N)`: Un cliente puede hacer múltiples pedidos.
- **Contiene** `(1,N)-(N,1)`: Un pedido puede contener múltiples libros y cada libro puede estar en varios pedidos.
  - Atributos: `Cantidad`, `Valor`
- **Existe** `(1,N)-(0,1)`: Verifica la existencia de un libro en inventario de una editorial.
- **Pertenece** `(1,N)-(1,1)`: Cada libro pertenece a una única editorial, pero una editorial puede tener múltiples libros.

## 📐 Consideraciones del Modelo
- Soporte para múltiples tipos de cliente (natural y jurídico).
- Registro detallado de libros y pedidos.
- Gestión de inventario por editorial.
- Separación lógica y clara de las entidades y sus relaciones.

## 📎 Licencia
Este modelo es de uso académico y puede ser adaptado para propósitos comerciales o personales bajo licencia MIT.





-- Tabla CLIENTE
CREATE TABLE Cliente (
    Codigo_cliente INT PRIMARY KEY,
    Tipo_ID CHAR(2) CHECK (Tipo_ID IN ('PN', 'PJ')),
    DNI VARCHAR(20),
    RUT VARCHAR(20),
    NIT VARCHAR(20),
    RUES VARCHAR(20),
    Nombre VARCHAR(100),
    Calle VARCHAR(100),
    Barrio VARCHAR(100),
    Ciudad VARCHAR(100),
    Estado VARCHAR(100),
    Telefono VARCHAR(20),
    Email VARCHAR(100),
    CHECK (
        (Tipo_ID = 'PN' AND DNI IS NOT NULL AND RUT IS NOT NULL AND NIT IS NULL AND RUES IS NULL) OR
        (Tipo_ID = 'PJ' AND NIT IS NOT NULL AND RUES IS NOT NULL AND DNI IS NULL AND RUT IS NULL)
    )
);

-- Tabla PEDIDO
CREATE TABLE Pedido (
    Codigo_pedido INT PRIMARY KEY,
    Codigo_cliente INT,
    Fecha DATE,
    Valor DECIMAL(10, 2),
    FOREIGN KEY (Codigo_cliente) REFERENCES Cliente(Codigo_cliente)
);

-- Tabla EDITORIAL
CREATE TABLE Editorial (
    Codigo_editorial INT PRIMARY KEY,
    Nombre_contacto VARCHAR(100),
    Telefono_1 VARCHAR(20),
    Telefono_2 VARCHAR(20),
    Email VARCHAR(100)
);

-- Tabla LIBRO
CREATE TABLE Libro (
    Codigo_libro INT PRIMARY KEY,
    Titulo VARCHAR(200),
    Categoria VARCHAR(100),
    ISBN VARCHAR(20),
    Año_publicacion INT,
    Editorial_nombre VARCHAR(100),
    Valor DECIMAL(10, 2),
    Autoria VARCHAR(100),
    Codigo_editorial INT,
    FOREIGN KEY (Codigo_editorial) REFERENCES Editorial(Codigo_editorial)
);

-- Tabla INVENTARIO
CREATE TABLE Inventario (
    Codigo_libro INT,
    Codigo_editorial INT,
    Cantidad INT,
    PRIMARY KEY (Codigo_libro, Codigo_editorial),
    FOREIGN KEY (Codigo_libro) REFERENCES Libro(Codigo_libro),
    FOREIGN KEY (Codigo_editorial) REFERENCES Editorial(Codigo_editorial)
);

-- Tabla CONTIENE (relación Pedido - Libro)
CREATE TABLE Contiene (
    Codigo_pedido INT,
    Codigo_libro INT,
    Cantidad INT,
    Valor DECIMAL(10, 2),
    PRIMARY KEY (Codigo_pedido, Codigo_libro),
    FOREIGN KEY (Codigo_pedido) REFERENCES Pedido(Codigo_pedido),
    FOREIGN KEY (Codigo_libro) REFERENCES Libro(Codigo_libro)
);

