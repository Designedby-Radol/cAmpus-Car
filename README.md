# Sistema de Gestión para Concesionario de Vehículos

## Descripción General

Este sistema de base de datos está diseñado para gestionar todas las operaciones de un concesionario de vehículos, permitiendo el registro y administración de vehículos en inventario, clientes, vendedores, transacciones de ventas y servicios de mantenimiento realizados.

## Diagrama Entidad-Relación

El siguiente diagrama muestra la estructura de la base de datos y las relaciones entre entidades:

![Diagrama Entidad-Relación](https://www.mermaidchart.com/raw/d4fd6213-93ef-472c-a00b-3247475fc3dc?theme=light&version=v0.1&format=svg)

También puede visualizarse en código Mermaid:

```mermaid
erDiagram
    VEHICULOS {
        bigint id PK
        text marca
        text modelo
        int anio
        text vin UK
        numeric precio
        text color
        text tipo_combustible
        text tipo_transmision
        text estado
        boolean disponibilidad
    }
    
    CLIENTES {
        bigint id PK
        text nombre
        text telefono
        text correo
        text direccion
    }
    
    VENDEDORES {
        bigint id PK
        text nombre
        text numero_empleado UK
        date fecha_contratacion
    }
    
    VENTAS {
        bigint id PK
        bigint cliente_id FK
        bigint vendedor_id FK
        date fecha_venta
        numeric monto_total
        text metodo_pago
    }
    
    DETALLES_DE_VENTA {
        bigint venta_id PK,FK
        bigint vehiculo_id PK,FK
    }
    
    MANTENIMIENTOS {
        bigint id PK
        bigint vehiculo_id FK
        bigint cliente_id FK
        text tipo_servicio
        numeric costo
        date fecha_servicio
    }
    
    CLIENTES ||--o{ VENTAS : "realiza"
    VENDEDORES ||--o{ VENTAS : "gestiona"
    VENTAS ||--o{ DETALLES_DE_VENTA : "incluye"
    VEHICULOS ||--o{ DETALLES_DE_VENTA : "es_vendido_en"
    VEHICULOS ||--o{ MANTENIMIENTOS : "recibe"
    CLIENTES ||--o{ MANTENIMIENTOS : "solicita"
## Estructura de la Base de Datos

### Tablas Principales

#### 1. Vehículos (`vehiculos`)
Almacena información detallada de todos los vehículos en el concesionario.

| Campo               | Tipo       | Descripción                                        |
|---------------------|------------|---------------------------------------------------|
| id                  | bigint     | Identificador único, autogenerado                 |
| marca               | text       | Marca del vehículo                                |
| modelo              | text       | Modelo del vehículo                               |
| anio                | int        | Año de fabricación                                |
| vin                 | text       | Número de identificación del vehículo (único)     |
| precio              | numeric    | Precio del vehículo                               |
| color               | text       | Color del vehículo                                |
| tipo_combustible    | text       | Tipo de combustible                               |
| tipo_transmision    | text       | Tipo de transmisión                               |
| estado              | text       | Estado ('nuevo', 'usado', 'no disponible')        |
| disponibilidad      | boolean    | Disponibilidad en inventario                      |

#### 2. Clientes (`clientes`)
Registra la información de contacto de los clientes.

| Campo     | Tipo   | Descripción                   |
|-----------|--------|-------------------------------|
| id        | bigint | Identificador único, autogenerado |
| nombre    | text   | Nombre completo del cliente   |
| telefono  | text   | Número telefónico             |
| correo    | text   | Correo electrónico            |
| direccion | text   | Dirección                     |

#### 3. Vendedores (`vendedores`)
Almacena información sobre los vendedores del concesionario.

| Campo             | Tipo   | Descripción                       |
|-------------------|--------|-----------------------------------|
| id                | bigint | Identificador único, autogenerado |
| nombre            | text   | Nombre completo del vendedor      |
| numero_empleado   | text   | Número de empleado (único)        |
| fecha_contratacion| date   | Fecha de contratación             |

#### 4. Ventas (`ventas`)
Registra las transacciones de venta realizadas.

| Campo           | Tipo        | Descripción                       |
|-----------------|-------------|-----------------------------------|
| id              | bigint      | Identificador único, autogenerado |
| cliente_id      | bigint (FK) | Cliente que realizó la compra     |
| vendedor_id     | bigint (FK) | Vendedor que procesó la venta     |
| fecha_venta     | date        | Fecha de la venta                 |
| monto_total     | numeric     | Monto total de la venta           |
| metodo_pago     | text        | Método de pago                    |

#### 5. Relación Ventas-Vehículos (`detalles_de_venta`)
Tabla de relación muchos a muchos entre ventas y vehículos.

| Campo         | Tipo        | Descripción                    |
|---------------|-------------|--------------------------------|
| venta_id      | bigint (FK) | Identificador de la venta      |
| vehiculo_id   | bigint (FK) | Identificador del vehículo     |

#### 6. Mantenimiento (`mantenimientos`)
Registra los servicios de mantenimiento realizados.

| Campo         | Tipo        | Descripción                       |
|---------------|-------------|-----------------------------------|
| id            | bigint      | Identificador único, autogenerado |
| vehiculo_id   | bigint (FK) | Vehículo que recibió el servicio  |
| cliente_id    | bigint (FK) | Cliente que solicitó el servicio  |
| tipo_servicio | text        | Tipo de servicio                  |
| costo         | numeric     | Costo del servicio                |
| fecha_servicio| date        | Fecha del servicio                |

## Justificación del Diseño

### Decisiones de Diseño

1. **Estructura Normalizada**: La base de datos está diseñada siguiendo los principios de normalización para reducir la redundancia y mejorar la integridad de los datos.

2. **Relación Muchos a Muchos para Ventas y Vehículos**: Se implementó una tabla intermedia (`detalles_de_venta`) para representar la relación muchos a muchos entre ventas y vehículos, permitiendo que una venta pueda incluir múltiples vehículos y que un vehículo pueda estar asociado a diferentes ventas (aunque en la práctica, un vehículo normalmente sólo se vende una vez).

3. **Campos de Estado y Disponibilidad**: Para cumplir con el requisito de actualizar el estado de los vehículos vendidos, se incluyeron los campos `estado` y `disponibilidad` en la tabla de vehículos.

4. **Mantenimiento Independiente**: La tabla de mantenimiento se diseñó para permitir registrar servicios tanto para vehículos vendidos (asociados a un cliente) como para vehículos aún en inventario (sin cliente asociado).

### Restricciones y Validaciones

1. **Claves Primarias**: Todas las tablas utilizan IDs auto-incrementables como claves primarias, excepto la tabla `detalles_de_venta` que utiliza una clave primaria compuesta por `venta_id` y `vehiculo_id`.

2. **Claves Foráneas**: 
   - `ventas.cliente_id` → `clientes.id`
   - `ventas.vendedor_id` → `vendedores.id`
   - `detalles_de_venta.venta_id` → `ventas.id`
   - `detalles_de_venta.vehiculo_id` → `vehiculos.id`
   - `mantenimientos.vehiculo_id` → `vehiculos.id`
   - `mantenimientos.cliente_id` → `clientes.id`

3. **Restricciones de Unicidad**:
   - `vehiculos.vin`: Garantiza que cada vehículo tenga un VIN único
   - `vendedores.numero_empleado`: Asegura que cada vendedor tenga un número de empleado único

4. **Restricciones de Verificación**:
   - `vehiculos.estado`: Solo puede tomar los valores 'nuevo', 'usado', o 'no disponible'

### Automatización de Procesos

Se implementó un trigger (`after_sale_insert`) que se activa después de insertar un registro en la tabla `detalles_de_venta`, actualizando automáticamente el estado y la disponibilidad del vehículo vendido:

```sql
create or replace function update_vehicle_availability() returns trigger as $$
BEGIN
    UPDATE vehiculos SET disponibilidad = false, estado = 'no disponible' WHERE id = NEW.vehiculo_id;
    RETURN NEW;
END;
$$ language plpgsql;

create trigger after_sale_insert
after insert on detalles_de_venta for each row
execute function update_vehicle_availability();

## Relaciones entre Entidades

1. **Clientes y Ventas (1:N)**:
   - Un cliente puede realizar múltiples compras.
   - Cada venta está asociada a un único cliente.

2. **Vendedores y Ventas (1:N)**:
   - Un vendedor puede gestionar múltiples ventas.
   - Cada venta es gestionada por un único vendedor.

3. **Ventas y Vehículos (N:M)**:
   - Una venta puede incluir múltiples vehículos.
   - Un vehículo podría estar asociado a múltiples ventas (aunque normalmente solo a una).
   - Esta relación se implementa mediante la tabla intermedia `detalles_de_venta`.

4. **Vehículos y Mantenimiento (1:N)**:
   - Un vehículo puede recibir múltiples servicios de mantenimiento.
   - Cada servicio de mantenimiento está asociado a un único vehículo.

5. **Clientes y Mantenimiento (1:N)**:
   - Un cliente puede solicitar múltiples servicios de mantenimiento.
   - Cada servicio está asociado a un único cliente (o a ninguno si el vehículo aún no ha sido vendido).

## Implementacion
El esquema SQL completo para crear esta base de datos es el siguiente:

´´´
create table vehiculos (
  id bigint primary key generated always as identity,
  marca text not null,
  modelo text not null,
  anio int not null,
  vin text not null unique,
  precio numeric(10, 2) not null,
  color text not null,
  tipo_combustible text not null,
  tipo_transmision text not null,
  estado text not null check (estado in ('nuevo', 'usado', 'no disponible')),
  disponibilidad boolean not null default true
);

create table clientes (
  id bigint primary key generated always as identity,
  nombre text not null,
  telefono text not null,
  correo text not null,
  direccion text not null
);

create table vendedores (
  id bigint primary key generated always as identity,
  nombre text not null,
  numero_empleado text not null unique,
  fecha_contratacion date not null
);

create table ventas (
  id bigint primary key generated always as identity,
  cliente_id bigint references clientes (id),
  vendedor_id bigint references vendedores (id),
  fecha_venta date not null,
  monto_total numeric(10, 2) not null,
  metodo_pago text not null
);

create table detalles_de_venta (
  venta_id bigint references ventas (id),
  vehiculo_id bigint references vehiculos (id),
  primary key (venta_id, vehiculo_id)
);

create table mantenimientos (
  id bigint primary key generated always as identity,
  vehiculo_id bigint references vehiculos (id),
  cliente_id bigint references clientes (id),
  tipo_servicio text not null,
  costo numeric(10, 2) not null,
  fecha_servicio date not null
);

create or replace function update_vehicle_availability() returns trigger as $$
BEGIN
    UPDATE vehiculos SET disponibilidad = false, estado = 'no disponible' WHERE id = NEW.vehiculo_id;
    RETURN NEW;
END;
$$ language plpgsql;

create trigger after_sale_insert
after insert on detalles_de_venta for each row
execute function update_vehicle_availability();

´´´
