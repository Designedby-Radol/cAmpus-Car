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
