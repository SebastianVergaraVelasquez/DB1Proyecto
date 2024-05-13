**Creación de la base de datos y tablas**

![alt text](erd_garden.jpg)


```sql
drop database IF EXISTS gardenDatabase;
CREATE DATABASE gardenDatabase;

USE gardenDatabase;

CREATE TABLE gama_producto (
id INT(11) NOT NULL PRIMARY KEY,
gama VARCHAR (50) NOT NULL,
descripcion_texto TEXT,
descripcion_html TEXT,
imagen VARCHAR(256),
CONSTRAINT UK_gama UNIQUE (gama)
);

CREATE TABLE producto (
	id VARCHAR (15) NOT NULL PRIMARY KEY,
    nombre VARCHAR (70) NOT NULL,
    id_gama INT NOT NULL,
    alto double,
    ancho double,
    largo double,
    descripcion TEXT,
    cantidad_en_stock SMALLINT (6) NOT NULL,
    precio_venta DECIMAL (15,2) NOT NULL,
    CONSTRAINT UK_nombre UNIQUE (nombre),
    FOREIGN KEY (id_gama) REFERENCES gama_producto (id) 
);

CREATE TABLE proveedor (
	nif VARCHAR(9) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL,
    telefono VARCHAR (15)
);

CREATE TABLE detalle_compra (
	id_producto VARCHAR (15) NOT NULL,
    id_proveedor VARCHAR  (9) NOT NULL,
    valor_unitario DECIMAL (15,2) NOT NULL,
    cantidad SMALLINT (6) NOT NULL,
    PRIMARY KEY (id_producto, id_proveedor),
    constraint FK_idProducto foreign key (id_producto) REFERENCES producto (id),
    constraint FK_idProveedor foreign key (id_proveedor) REFERENCES proveedor (nif)
);

CREATE TABLE cliente (
	id INT (11) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL,
    apellido  VARCHAR (50) NOT NULL,
    fax VARCHAR (15),
    codigo_postal VARCHAR (10),
    id_empleado_rep_ventas INT (10),
    limite_credito DECIMAL (15,2)
);

CREATE TABLE pedido (
	id INT (11) NOT NULL PRIMARY KEY,
    fecha_pedido DATE NOT NULL,
    fecha_esperada DATE NOT NULL,
    fecha_entrega DATE,
    id_estado TINYINT NOT NULL,
    comentarios TEXT,
    id_cliente INT (11) NOT NULL
);

CREATE TABLE detalle_pedido(
	id_pedido INT (11) NOT NULL,
    id_producto VARCHAR (15) NOT NULL,
    cantidad INT (11),
    precio_unidad DECIMAL (15,2),
    PRIMARY KEY (id_pedido, id_producto),
    constraint FK_idPedido foreign key (id_pedido) REFERENCES pedido (id),
    constraint FK_idproductoPedido foreign key (id_producto) REFERENCES producto (id)
);

CREATE TABLE estado(
	id TINYINT NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL
);

CREATE TABLE pais(
	id VARCHAR(3) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL
);

CREATE TABLE region(
	id VARCHAR (2) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL,
    id_pais VARCHAR(3) NOT NULL,
    constraint FK_PaisRegion foreign key (id_pais) REFERENCES pais (id)
);

CREATE TABLE ciudad(
	id VARCHAR (5) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL,
    id_region VARCHAR (2) NOT NULL,
    constraint FK_ciudadRegion foreign key (id_region) REFERENCES region (id)
);

CREATE TABLE contacto (
	id INT (11) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL,
    apellido VARCHAR (50) NOT NULL,
    telefono VARCHAR (15) NOT NULL,
    email VARCHAR (30),
    id_cliente INT(11) NOT NULL,
    CONSTRAINT FK_contactoCliente FOREIGN KEY (id_cliente) REFERENCES cliente (id)
);

CREATE TABLE tipo_direccion (
 id INT (11) NOT NULL PRIMARY KEY,
 nombre VARCHAR (20) NOT NULL
);

CREATE TABLE direccion_Cliente(
	id INT (11) NOT NULL PRIMARY KEY,
    descripcion VARCHAR (100) NOT NULL,
    id_cliente INT (11) NOT NULL,
    id_ciudad VARCHAR (5) NOT NULL,
    id_tipo_direccion INT (11) NOT NULL,
    CONSTRAINT FK_direccionCliente FOREIGN KEY (id_cliente) REFERENCES cliente (id),
    CONSTRAINT FK_direccionCiudad FOREIGN KEY (id_ciudad) REFERENCES ciudad (id),
    CONSTRAINT FK_direccionTipo FOREIGN KEY (id_tipo_direccion) REFERENCES tipo_direccion (id)
);

CREATE TABLE tipo_telefono (
	id INT (11) NOT NULL PRIMARY KEY,
	nombre VARCHAR (20) NOT NULL
);

CREATE TABLE telefono_Cliente(
	id INT (11) NOT NULL PRIMARY KEY,
	telefono VARCHAR (15) NOT NULL,
    id_cliente INT (11) NOT NULL,
    id_tipo_telefono INT (11) NOT NULL,
    CONSTRAINT FK_telefonoCliente FOREIGN KEY (id_cliente) REFERENCES cliente (id),
    CONSTRAINT FK_telefonoTipo FOREIGN KEY (id_tipo_telefono) REFERENCES tipo_telefono (id)
);

CREATE TABLE tipo_pago (
 id INT (11) NOT NULL PRIMARY KEY,
 nombre VARCHAR (20) NOT NULL
);

CREATE TABLE pago(
	id int (11) NOT NULL PRIMARY KEY,
    id_tipo_pago INT (11) NOT NULL,
    id_cliente INT (11) NOT NULL,
    fecha DATE NOT NULL,
    total DECIMAL (15,2),
    CONSTRAINT FK_clientePagp FOREIGN KEY (id_cliente) REFERENCES cliente (id),
    CONSTRAINT FK_pagoTipo FOREIGN KEY (id_tipo_pago) REFERENCES tipo_pago (id)
);

CREATE TABLE empleado (
	id INT (11) NOT NULL PRIMARY KEY,
    nombre VARCHAR (50) NOT NULL,
    apellido1 VARCHAR (50) NOT NULL,
    apellido2  VARCHAR (50) NOT NULL,
    extension VARCHAR (10),
    email VARCHAR (100) NOT NULL,
    id_oficina VARCHAR (10) NOT NULL,
    id_jefe INT(11),
    puesto VARCHAR (50) NOT NULL,
    CONSTRAINT FK_jefeEmpleado FOREIGN KEY (id_jefe) REFERENCES empleado (id) 
);

CREATE TABLE oficina (
	id VARCHAR (10) NOT NULL PRIMARY KEY,
    codigo_postal VARCHAR (10)
);

CREATE TABLE direccion_oficina (
    id INT(11) NOT NULL PRIMARY KEY,
    descripcion VARCHAR(100) NOT NULL,
    id_oficina VARCHAR (10) NOT NULL,
    id_ciudad VARCHAR (5) NOT NULL,
    id_tipo_direccion INT(11) NOT NULL,
    FOREIGN KEY (id_oficina) REFERENCES oficina (id),
    CONSTRAINT FK_direccionCiudadOfi FOREIGN KEY (id_ciudad) REFERENCES ciudad (id),
    CONSTRAINT FK_direccionTipoOfi FOREIGN KEY (id_tipo_direccion) REFERENCES tipo_direccion (id)
);

CREATE TABLE telefono_oficina(
	id INT (11) NOT NULL PRIMARY KEY,
	telefono VARCHAR (15) NOT NULL,
    id_oficina VARCHAR (10) NOT NULL,
    id_tipo_telefono INT (11) NOT NULL,
    CONSTRAINT FK_telefonoOficina FOREIGN KEY (id_oficina) REFERENCES oficina (id),
    CONSTRAINT FK_telefonoTipoOfi FOREIGN KEY (id_tipo_telefono) REFERENCES tipo_telefono (id)
);

ALTER TABLE empleado
ADD CONSTRAINT FK_oficinaEmpleado FOREIGN KEY (id_oficina) REFERENCES oficina (id);

ALTER TABLE cliente
ADD CONSTRAINT FK_clienteEmpleado FOREIGN KEY (id_empleado_rep_ventas) REFERENCES empleado (id);

ALTER TABLE pedido
ADD CONSTRAINT FK_estadoPedido FOREIGN KEY (id_estado) REFERENCES estado (id);

```

**nserción de datos**

```sql
INSERT INTO gama_producto (id, gama, descripcion_texto, descripcion_html, imagen)  
VALUES  (1, 'Baja', 'Productos de gama baja', 'Productos de gama baja', 'baja.jpg'), 
(2, 'Media', 'Productos de gama media', 'Productos de gama media', 'media.jpg'), 
(3, 'Alta', 'Productos de gama alta', 'Productos de gama alta', 'alta.jpg'); 

INSERT INTO producto (id, nombre, id_gama, alto, ancho, largo, descripcion, cantidad_en_stock, precio_venta)
VALUES 
('S10_1678', '1969 Harley Davidson Ultimate Chopper', 3, 15.0, 6.0, 10.25, 'This replica features working kickstand, front suspension, gear-shift lever', 7933, 48.81),
('S10_1949', '1952 Alpine Renault 1300', 1, 10.25, 8.0, 6.0, 'Turnable front wheels; steering function; detailed interior; detailed engine; opening hood', 7305, 98.58),
('S10_2016', '1996 Moto Guzzi 1100i', 1, 9.0, 3.25, 7.75, 'Official Moto Guzzi logos and insignias, saddle bags located on side of motorcycle', 6625, 68.99),
('S10_4698', '2003 Harley-Davidson Eagle Drag Bike', 3, 15.0, 6.0, 10.25, 'Model features, official Harley Davidson logos and insignias, detachable rear wheelie bar', 5582, 91.02),
('S12_1099', '1968 Ford Mustang', 1, 9.0, 5.0, 13.38, 'Hood, doors and trunk all open to reveal highly detailed interior features.', 68, 95.34),
('S12_2823', '2002 Suzuki XREO', 3, 15.0, 6.0, 10.25, 'Official logos and insignias, saddle bags located on side of motorcycle, detailed engine, working steering, working suspension', 9997, 66.27),
('S12_3990', '1970 Plymouth Hemi Cuda', 2, 19.0, 8.0, 13.0, 'This model features working steering, suspension, opening doors, opening hood, and detailed engine', 5663, 31.92),
('S12_4473', '1957 Chevy Pickup', 1, 10.25, 7.0, 7.75, 'Detailed scale replica with working suspension and opening doors.', 6125, 55.70),
('S12_4675', '1969 Dodge Charger', 2, 19.0, 8.0, 13.0, 'Detailed model of the 1969 Dodge Charger. This model includes 440 Magnum engine', 7323, 58.73),
('S18_1129', '1993 Mazda RX-7', 2, 9.0, 5.0, 13.0, 'This model features, opening hood, opening doors, detailed engine, rear spoiler, opening trunk, working steering', 3975, 83.51);

INSERT INTO proveedor (nif, nombre, telefono) 
VALUES 
('123456789', 'Classic Models Inc.', '555-555-555'),
('987654321', 'Auto-Moto Classics Inc.', '123-456-789'),
('456789123', 'Motor City Art Classics', '321-654-987');

INSERT INTO detalle_compra (id_producto, id_proveedor, valor_unitario, cantidad)
VALUES 
('S10_1678', '123456789', 48.81, 5),
('S10_1949', '987654321', 98.58, 3),
('S10_2016', '456789123', 68.99, 2),
('S10_4698', '123456789', 91.02, 4),
('S12_1099', '987654321', 95.34, 6),
('S12_2823', '456789123', 66.27, 1),
('S12_3990', '123456789', 31.92, 8),
('S12_4473', '987654321', 55.70, 7),
('S12_4675', '456789123', 58.73, 9),
('S18_1129', '123456789', 83.51, 10);

INSERT INTO estado (id, nombre)
VALUES 
(1, 'En proceso'),
(2, 'Pendiente'),
(3, 'Entregado'),
(4, 'Rechazado');

INSERT INTO pais (id, nombre)
VALUES 
('001', 'Estados Unidos'),
('002', 'Canadá'),
('003', 'México');

INSERT INTO region (id, nombre, id_pais)
VALUES 
('10', 'California', '001'),
('20', 'Quebec', '002'),
('30', 'Jalisco', '003');

INSERT INTO ciudad (id, nombre, id_region)
VALUES 
('10101', 'Los Angeles', '10'),
('20202', 'Montreal', '20'),
('30303', 'Guadalajara', '30');

INSERT INTO tipo_direccion (id, nombre)
VALUES 
(1, 'Principal'),
(2, 'Facturación');

INSERT INTO tipo_telefono (id, nombre)
VALUES 
(1, 'Casa'),
(2, 'Trabajo'),
(3, 'Móvil');

INSERT INTO tipo_pago (id, nombre)
VALUES 
(1, 'Tarjeta de crédito'),
(2, 'Transferencia'),
(3, 'Cheque'),
(4, 'Efectivo');

INSERT INTO oficina (id,codigo_postal)
VALUES 
('1', '90003'),
('2', 'H1J 1C3'),
('3', '44670'),
('4', '90003'),
('6', 'H1J 1C3'),
('7', '44670');

INSERT INTO direccion_oficina (id, descripcion, id_oficina, id_ciudad, id_tipo_direccion)
VALUES 
(1, '100 Market Street', '1', '10101', 1),
(2, '1550 Court Place', '2', '20202', 1),
(3, '2678 Kingston Road', '3', '30303', 1),
(4, '100 Market Street', '4', '10101', 1),
(5, '1550 Court Place', '6', '20202', 1),
(6, '2678 Kingston Road', '7', '30303', 1);

INSERT INTO telefono_oficina (id, telefono, id_oficina, id_tipo_telefono)
VALUES 
(1, '555-555-0100', '1', 1),
(2, '555-555-0101', '2', 1),
(3, '555-555-0102', '3', 1),
(4, '555-555-0100', '4', 1),
(5, '555-555-0101', '6', 1),
(6, '555-555-0102', '7', 1);

INSERT INTO empleado (id, nombre, apellido1, apellido2, extension, email, id_oficina, id_jefe, puesto)
VALUES 
(1002, 'Diane', 'Murphy', '', 'x5800', 'dmurphy@classicmodelcars.com', '1', NULL, 'Presidente'),
(1056, 'Mary', 'Patterson', '', 'x4611', 'mpatterso@classicmodelcars.com', '1', 1002, 'Vicepresidente de ventas'),
(1076, 'Jeff', 'Firrelli', '', 'x9273', 'jfirrelli@classicmodelcars.com', '1', 1056, 'Gerente de ventas'),
(1088, 'William', 'Patterson', '', 'x4871', 'wpatterson@classicmodelcars.com', '6', 1002, 'Gerente de ventas (APAC)'),
(1102, 'Barry', 'Jones', '', 'x102', 'bjones@classicmodelcars.com', '1', 1056, 'Gerente de ventas (NA)'),
(1143, 'Andy', 'Fixter', '', 'x101', 'afixter@classicmodelcars.com', '1', 1102, 'Vendedor'),
(1165, 'Leslie', 'Jennings', '', 'x102', 'ljennings@classicmodelcars.com', '1', 1102, 'Vendedor'),
(1166, 'Leslie', 'Thompson', '', 'x102', 'lthompson@classicmodelcars.com', '1', 1102, 'Vendedor'),
(1188, 'Julie', 'Firrelli', '', 'x2173', 'jfirrelli@classicmodelcars.com', '2', 1056, 'Gerente de ventas'),
(1216, 'Steve', 'Patterson', '', 'x4334', 'spatterson@classicmodelcars.com', '2', 1188, 'Vendedor'),
(1286, 'Foon Yue', 'Tseng', '', 'x2248', 'ftseng@classicmodelcars.com', '3', 1143, 'Vendedor'),
(1323, 'George', 'Vanauf', '', 'x4102', 'gvanauf@classicmodelcars.com', '3', 1143, 'Vendedor'),
(1337, 'Loui', 'Bondur', '', 'x6493', 'lbondur@classicmodelcars.com', '4', 1102, 'Gerente de ventas (EMEA)'),
(1370, 'Gerard', 'Hernandez', '', 'x2028', 'ghernande@classicmodelcars.com', '4', 1337, 'Vendedor'),
(1401, 'Pamela', 'Castillo', '', 'x2759', 'pcastillo@classicmodelcars.com', '4', 1337, 'Vendedor'),
(1501, 'Larry', 'Bott', '', 'x2311', 'lbott@classicmodelcars.com', '7', 1102, 'Gerente de ventas (APAC)'),
(1504, 'Barry', 'Jones', '', 'x4782', 'bjones@classicmodelcars.com', '7', 1501, 'Vendedor');

INSERT INTO cliente (id, nombre, apellido, fax, codigo_postal, id_empleado_rep_ventas, limite_credito)
VALUES 
(1, 'Ernst', 'Handel', '123456789', '12209', 1370, 1370.00),
(2, 'Wilhelm', 'Bergulfsen', '987654321', '4110', 1504, 1504.00),
(3, 'Roland', 'Mendel', '456789123', '8010', 1188, 1188.00),
(4, 'Helen', 'Bennett', '123456789', '5010', 1165, 1165.00),
(5, 'Maria', 'Anders', '987654321', '5020', 1401, 1401.00),
(6, 'Francisco', 'Chang', '456789123', '5022', 1216, 1621.00),
(7, 'Yang', 'Wang', '123456789', '7100', 1501, 1501.00),
(8, 'Pedro', 'Afonso', '987654321', '7800', 1401, 1400.00),
(9, 'Anna', 'Bedecs', '456789123', '10100', 1088, 1800.00),
(10, 'Susan', 'Nelson', '123456789', '6010', 1504, 1500.00);

INSERT INTO direccion_Cliente (id, descripcion, id_cliente, id_ciudad, id_tipo_direccion)
VALUES 
(1, '123 Main Street', 1, '10101', 1),
(2, '567 1st Ave', 2, '20202', 1),
(3, '890 Broadway', 3, '30303', 1),
(4, '123 Pine Street', 4, '10101', 1),
(5, '456 Oak Street', 5, '20202', 1),
(6, '789 Elm Street', 6, '30303', 1),
(7, '321 Cedar Street', 7, '10101', 1),
(8, '654 Birch Street', 8, '20202', 1),
(9, '987 Maple Street', 9, '30303', 1),
(10, '1010 Spruce Street', 10, '10101', 1);


INSERT INTO telefono_Cliente (id, telefono, id_cliente, id_tipo_telefono)
VALUES 
(1, '555-555-1234', 1, 1),
(2, '555-555-9876', 2, 1),
(3, '555-555-5678', 3, 1),
(4, '555-555-8765', 4, 1),
(5, '555-555-7654', 5, 1),
(6, '555-555-4321', 6, 1),
(7, '555-555-1111', 7, 1),
(8, '555-555-2222', 8, 1),
(9, '555-555-3333', 9, 1),
(10, '555-555-4444', 10, 1);

INSERT INTO contacto (id, nombre, apellido, telefono, email, id_cliente)
VALUES 
(1, 'Paula', 'Wilson', '555-1234', 'paula@classicmodelcars.com', 1),
(2, 'Mary', 'Young', '555-9876', 'mary@classicmodelcars.com', 2),
(3, 'Michael', 'Young', '555-5678', 'michael@classicmodelcars.com', 3),
(4, 'Anna', 'Fritz', '555-8765', 'anna@classicmodelcars.com', 4),
(5, 'Susie', 'King', '555-7654', 'susie@classicmodelcars.com', 5),
(6, 'Tom', 'King', '555-4321', 'tom@classicmodelcars.com', 6),
(7, 'Pedro', 'García', '555-1111', 'pedro@classicmodelcars.com', 7),
(8, 'Manuel', 'Martinez', '555-2222', 'manuel@classicmodelcars.com', 8),
(9, 'Luisa', 'López', '555-3333', 'luisa@classicmodelcars.com', 9),
(10, 'Javier', 'Ruiz', '555-4444', 'javier@classicmodelcars.com', 10);

INSERT INTO pago (id, id_tipo_pago, id_cliente, fecha, total)
VALUES 
(1, 1, 1, '2023-01-10', 488.10),
(2, 2, 2, '2023-01-11', 1064.67),
(3, 3, 3, '2023-01-12', 1380.00),
(4, 4, 4, '2023-01-13', 1165.00),
(5, 1, 5, '2023-01-14', 1401.00),
(6, 2, 6, '2023-01-15', 1621.00),
(7, 3, 7, '2023-01-16', 1501.00),
(8, 4, 8, '2023-01-17', 1400.00),
(9, 1, 9, '2023-01-18', 1800.00),
(10, 2, 10, '2023-01-19', 1500.00);

INSERT INTO pedido (id, fecha_pedido, fecha_esperada, fecha_entrega, id_estado, comentarios, id_cliente)
VALUES 
(10100, '2023-01-01', '2023-01-15', '2023-01-10', 1, 'Confirmado', 1),
(10101, '2023-01-02', '2023-01-16', NULL, 2, 'En espera', 2),
(10102, '2023-01-03', '2023-01-17', '2023-01-14', 3, 'Entregado', 3),
(10103, '2023-01-04', '2023-01-18', NULL, 1, 'Confirmado', 4),
(10104, '2023-01-05', '2023-01-19', '2023-01-20', 2, 'Entrega retrasada', 5),
(10105, '2023-01-06', '2023-01-20', NULL, 3, 'En espera', 6),
(10106, '2023-01-07', '2023-01-21', '2023-01-25', 1, 'Entregado parcialmente', 7),
(10107, '2023-01-08', '2023-01-22', NULL, 2, 'En espera', 8),
(10108, '2023-01-09', '2023-01-23', '2023-01-24', 3, 'Confirmado', 9),
(10109, '2023-01-10', '2023-01-24', '2023-01-28', 1, 'Entregado', 10);

INSERT INTO detalle_pedido (id_pedido, id_producto, cantidad, precio_unidad)
VALUES 
(10100, 'S10_1678', 2, 48.81),
(10100, 'S10_1949', 1, 98.58),
(10101, 'S10_2016', 3, 68.99),
(10101, 'S10_4698', 2, 91.02),
(10102, 'S12_1099', 1, 95.34),
(10102, 'S12_2823', 3, 66.27),
(10103, 'S12_3990', 1, 31.92),
(10103, 'S12_4473', 2, 55.70),
(10104, 'S12_4675', 4, 58.73),
(10104, 'S18_1129', 2, 83.51);

```

**Normalización**

**1FN**

- La información de cada tabla no depende del orden en la cuál se ingreso cada registro
- Cada una de las tablas posee una llave primaria, y las  tablas intermedias como detalle_compra y detalle_pedido tienen una llave primaria compuesta.
- No existen grupo repetidos en ninguna de las tablas. En el caso de la información de proveedor que existía en producto, se asumió  que un producto podría tener más de un proveedor. Con el objetivo de  crear múltiples columnas de proveedor dentro de producto según fuera  necesario (proveedor 1, proveedor 2, proveedor 3...) se creó una tabla  de proveedor.

**2FN**

Cada atributo de cada una de las tablas está asociado  únicamente a dicha entidad, es decir, toda la información de cada tabla  corresponde específicamente a esa entidad.

**3FN**

En ninguna de las tablas existe dependencia de alguno de  sus atributos de manera indirecta hacia la llave primaria. Todos sus  campos dependen de la llave primaria y los campos que referencian  información de otras tablas se relacionan mediante un índice, evitando  generar inconsistencias a la hora de ingresar información. Por ejemplo  los códigos de estado en pedido.

**4FN**

No hay dependencias multivaluadas en las tablas.

**Consultas sobre una tabla**

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

   ```sql
   SELECT ofi.id, ciu.nombre 
   FROM oficina AS ofi
   INNER JOIN direccion_oficina do ON do.id_oficina = ofi.id
   INNER JOIN ciudad ciu ON ciu.id = do.id_ciudad;
   
   +----+-------------+
   | id | nombre      |
   +----+-------------+
   | 1  | Los Angeles |
   | 4  | Los Angeles |
   | 2  | Montreal    |
   | 6  | Montreal    |
   | 3  | Guadalajara |
   | 7  | Guadalajara |
   +----+-------------+
   ```

   ​    

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España

```sql
SELECT ci.nombre AS ciudad, tof.telefono AS telefono
FROM oficina o
JOIN direccion_oficina dof ON o.id = dof.id_oficina
JOIN ciudad ci ON dof.id_ciudad = ci.id
JOIN telefono_oficina tof ON o.id = tof.id_oficina
JOIN pais p ON ci.id_region = p.id
WHERE p.nombre = 'España';

Empty set 
```

​    

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

```sql
SELECT e.nombre, e.apellido1, e.apellido2, e.email
FROM empleado as e
WHERE e.id_jefe = 7;

Empty set
```

​    

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

```sql
SELECT e. puesto, e.nombre, e.apellido1, e.apellido2, e.email
FROM empleado as e
WHERE e.id_jefe IS NULL;

+------------+--------+-----------+-----------+------------------------------+
| puesto     | nombre | apellido1 | apellido2 | email                        |
+------------+--------+-----------+-----------+------------------------------+
| Presidente | Diane  | Murphy    |           | dmurphy@classicmodelcars.com |
+------------+--------+-----------+-----------+------------------------------+
```

​    

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

```sql
SELECT e.nombre, e.apellido1, e.apellido2, e.puesto
FROM empleado as e
WHERE e.puesto != 'Vendedor';

+---------+-----------+-----------+--------------------------+
| nombre  | apellido1 | apellido2 | puesto                   |
+---------+-----------+-----------+--------------------------+
| Diane   | Murphy    |           | Presidente               |
| Mary    | Patterson |           | Vicepresidente de ventas |
| Jeff    | Firrelli  |           | Gerente de ventas        |
| William | Patterson |           | Gerente de ventas (APAC) |
| Barry   | Jones     |           | Gerente de ventas (NA)   |
| Julie   | Firrelli  |           | Gerente de ventas        |
| Loui    | Bondur    |           | Gerente de ventas (EMEA) |
| Larry   | Bott      |           | Gerente de ventas (APAC) |
| Andy    | Fixter    |           | Gerente de ventas (NA)   |
+---------+-----------+-----------+--------------------------+
```

​    

6. Devuelve un listado con el nombre de los todos los clientes españoles.

```sql
SELECT c.nombre, c.apellido
FROM cliente AS c
JOIN direccion_Cliente ON direccion_Cliente.id_cliente = c.id
JOIN ciudad ON ciudad.id = direccion_Cliente.id_ciudad
JOIN region ON region.id = ciudad.id_region
JOIN pais ON pais.id = region.id_pais
WHERE pais.nombre = 'España';

Empty set
```

​    

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.

```sql
SELECT id, nombre
FROM estado;

+----+------------+
| id | nombre     |
+----+------------+
|  1 | En proceso |
|  2 | Pendiente  |
|  3 | Entregado  |
|  4 | Rechazado  |
+----+------------+

```

​    

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta: • Utilizando la función YEAR de MySQL. • Utilizando la función DATE_FORMAT de MySQL. • Sin utilizar ninguna de las funciones anteriores.

```sql
SELECT DISTINCT id_cliente
FROM pago
WHERE YEAR(fecha) = 2008;

SELECT DISTINCT id_cliente
FROM pago
WHERE DATE_FORMAT(fecha, '%Y') = '2008';

SELECT DISTINCT id_cliente
FROM pago
WHERE fecha >= '2008-01-01' AND fecha <= '2008-12-31';

Empty set
```

​    

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

```sql
SELECT id, id_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE fecha_entrega > fecha_esperada;

+-------+------------+----------------+---------------+
| id    | id_cliente | fecha_esperada | fecha_entrega |
+-------+------------+----------------+---------------+
| 10104 |          5 | 2023-01-19     | 2023-01-20    |
| 10106 |          7 | 2023-01-21     | 2023-01-25    |
| 10108 |          9 | 2023-01-23     | 2023-01-24    |
| 10109 |         10 | 2023-01-24     | 2023-01-28    |
+-------+------------+----------------+---------------+
```

​    

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada. • Utilizando la función ADDDATE de MySQL. • Utilizando la función DATEDIFF de MySQL. • ¿Sería posible resolver esta consulta utilizando el operador de suma + o resta -?

```sql
SELECT id, id_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE fecha_entrega < ADDDATE(fecha_esperada, -2);

SELECT id, id_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;

SELECT id, id_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE fecha_esperada - fecha_entrega >= INTERVAL 2 DAY;

+-------+------------+----------------+---------------+
| id    | id_cliente | fecha_esperada | fecha_entrega |
+-------+------------+----------------+---------------+
| 10100 |          1 | 2023-01-15     | 2023-01-10    |
| 10102 |          3 | 2023-01-17     | 2023-01-14    |
+-------+------------+----------------+---------------+
```

​    

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.

```sql
SELECT p.id, p.fecha_pedido, e.nombre, p.comentarios
FROM pedido as p, estado as e
WHERE p.id_estado = e.id AND YEAR(p.fecha_pedido) = 2009;

+------+--------------+-----------+------------------------------+
| id   | fecha_pedido | nombre    | comentarios                  |
+------+--------------+-----------+------------------------------+
| 1001 | 2009-01-01   | Rechazado | Pedido rechazado por cliente |
+------+--------------+-----------+------------------------------+
```

​    

12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.

```sql
SELECT p.id, p.fecha_pedido, e.nombre, p.comentarios
FROM pedido as p, estado as e
WHERE p.id_estado = e.id AND DATE_FORMAT(fecha_entrega, '%m') = '01' AND p.id_estado = 3;

+-------+--------------+-----------+-------------+
| id    | fecha_pedido | nombre    | comentarios |
+-------+--------------+-----------+-------------+
| 10102 | 2023-01-03   | Entregado | Entregado   |
| 10108 | 2023-01-09   | Entregado | Confirmado  |
+-------+--------------+-----------+-------------+
```

​    

13. Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.

```sql
SELECT p.id, tp.nombre, p.id_cliente, p.fecha, p.total
FROM pago AS p
JOIN tipo_pago AS tp ON tp.id = p.id_tipo_pago
WHERE tp.nombre = 'Paypal' AND YEAR(p.fecha) = '2008'
ORDER BY p.total DESC;

Empty set
```

​    

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.

```sql
SELECT id, nombre
FROM tipo_pago;

+----+---------------------+
| id | nombre              |
+----+---------------------+
|  1 | Tarjeta de crédito  |
|  2 | Transferencia       |
|  3 | Cheque              |
|  4 | Efectivo            |
+----+---------------------+
```

​    

15. Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

```sql
SELECT p.id, p.nombre, p.precio_venta, p.cantidad_en_stock
FROM producto AS p
JOIN gama_producto AS g ON p.id_gama = g.id
WHERE g.gama = 'Ornamentales' AND p.cantidad_en_stock > 100
ORDER BY p.precio_venta DESC;

Empty set
```

​    

16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.

```sql
SELECT cli.id, cli.nombre, cli.apellido, direccion_Cliente.descripcion, cli.codigo_postal
FROM cliente AS cli
JOIN direccion_Cliente ON direccion_Cliente.id_cliente = cli.id
JOIN ciudad ON ciudad.id = direccion_Cliente.id_ciudad
JOIN empleado ON empleado.id = cli.id_empleado_rep_ventas
WHERE ciudad.nombre = 'Madrid' AND empleado.id IN (11,30);

Empty set
```

​    

**Consultas multitabla (Composición interna)**

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

```sql
SELECT c.id, c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante
FROM cliente AS c
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id;

+----+-----------+------------+---------------------+-----------------------+
| id | nombre    | apellido   | NombreRepresentante | ApellidoRepresentante |
+----+-----------+------------+---------------------+-----------------------+
|  1 | Ernst     | Handel     | Gerard              | Hernandez             |
|  2 | Wilhelm   | Bergulfsen | Barry               | Jones                 |
|  3 | Roland    | Mendel     | Julie               | Firrelli              |
|  4 | Helen     | Bennett    | Leslie              | Jennings              |
|  5 | Maria     | Anders     | Pamela              | Castillo              |
|  6 | Francisco | Chang      | Steve               | Patterson             |
|  7 | Yang      | Wang       | Larry               | Bott                  |
|  8 | Pedro     | Afonso     | Pamela              | Castillo              |
|  9 | Anna      | Bedecs     | William             | Patterson             |
| 10 | Susan     | Nelson     | Barry               | Jones                 |
+----+-----------+------------+---------------------+-----------------------+

```

​    

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

```sql
SELECT c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante
FROM cliente AS c
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
JOIN pago AS p ON c.id = p.id_cliente;

+-----------+------------+---------------------+-----------------------+
| nombre    | apellido   | NombreRepresentante | ApellidoRepresentante |
+-----------+------------+---------------------+-----------------------+
| Ernst     | Handel     | Gerard              | Hernandez             |
| Wilhelm   | Bergulfsen | Barry               | Jones                 |
| Roland    | Mendel     | Julie               | Firrelli              |
| Helen     | Bennett    | Leslie              | Jennings              |
| Maria     | Anders     | Pamela              | Castillo              |
| Francisco | Chang      | Steve               | Patterson             |
| Yang      | Wang       | Larry               | Bott                  |
| Pedro     | Afonso     | Pamela              | Castillo              |
| Anna      | Bedecs     | William             | Patterson             |
| Susan     | Nelson     | Barry               | Jones                 |
+-----------+------------+---------------------+-----------------------+

```

​    

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

```sql
SELECT c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante
FROM cliente AS c
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
LEFT JOIN pago AS p ON c.id = p.id_cliente
WHERE p.id IS NULL;

Empty set
```

​    

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante, ciu.nombre AS CiudadOficina
FROM cliente AS c
JOIN pago AS p ON c.id = p.id_cliente
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
JOIN oficina AS o ON e.id_oficina = o.id
JOIN direccion_oficina AS dof ON dof.id_oficina = o.id
JOIN ciudad as ciu ON ciu.id = dof.id_ciudad;

+-----------+------------+---------------------+-----------------------+---------------+
| nombre    | apellido   | NombreRepresentante | ApellidoRepresentante | CiudadOficina |
+-----------+------------+---------------------+-----------------------+---------------+
| Helen     | Bennett    | Leslie              | Jennings              | Los Angeles   |
| Francisco | Chang      | Steve               | Patterson             | Montreal      |
| Roland    | Mendel     | Julie               | Firrelli              | Montreal      |
| Pedro     | Afonso     | Pamela              | Castillo              | Los Angeles   |
| Maria     | Anders     | Pamela              | Castillo              | Los Angeles   |
| Ernst     | Handel     | Gerard              | Hernandez             | Los Angeles   |
| Anna      | Bedecs     | William             | Patterson             | Montreal      |
| Susan     | Nelson     | Barry               | Jones                 | Guadalajara   |
| Yang      | Wang       | Larry               | Bott                  | Guadalajara   |
| Wilhelm   | Bergulfsen | Barry               | Jones                 | Guadalajara   |
+-----------+------------+---------------------+-----------------------+---------------+
```

​    

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante, ciu.nombre AS ciudaicina
FROM cliente AS c
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
LEFT JOIN pago AS p ON c.id = p.id_cliente
JOIN oficina AS o ON e.id_oficina = o.id
JOIN direccion_oficina AS dof ON dof.id_oficina = o.id
JOIN ciudad as ciu ON ciu.id = dof.id_ciudad
WHERE p.id IS NULL;

Empty set
```


6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.

```sql
SELECT dirOfi.descripcion AS DireccionOficina
FROM cliente AS c
JOIN direccion_Cliente AS dirCli ON c.id = dirCli.id_cliente
JOIN ciudad AS ciu_cliente ON dirCli.id_ciudad = ciu_cliente.id
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
JOIN oficina AS o ON e.id_oficina = o.id
JOIN direccion_oficina AS dirOfi ON o.id = dirOfi.id_oficina
JOIN ciudad AS ciu_oficina ON dirOfi.id_ciudad = ciu_oficina.id
WHERE ciu_cliente.nombre = 'Fuenlabrada';

Empty set
```

​    

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante, ciu_oficina.nombre AS ciudaicina
FROM cliente AS c
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
JOIN oficina AS o ON e.id_oficina = o.id
JOIN direccion_oficina AS dirOfi ON o.id = dirOfi.id_oficina
JOIN ciudad AS ciu_oficina ON dirOfi.id_ciudad = ciu_oficina.id;

+-----------+------------+---------------------+-----------------------+-------------+
| nombre    | apellido   | NombreRepresentante | ApellidoRepresentante | ciudaicina  |
+-----------+------------+---------------------+-----------------------+-------------+
| Pedro     | Afonso     | Pamela              | Castillo              | Los Angeles |
| Maria     | Anders     | Pamela              | Castillo              | Los Angeles |
| Helen     | Bennett    | Leslie              | Jennings              | Los Angeles |
| Ernst     | Handel     | Gerard              | Hernandez             | Los Angeles |
| Anna      | Bedecs     | William             | Patterson             | Montreal    |
| Francisco | Chang      | Steve               | Patterson             | Montreal    |
| Roland    | Mendel     | Julie               | Firrelli              | Montreal    |
| Susan     | Nelson     | Barry               | Jones                 | Guadalajara |
| Yang      | Wang       | Larry               | Bott                  | Guadalajara |
| Wilhelm   | Bergulfsen | Barry               | Jones                 | Guadalajara |
+-----------+------------+---------------------+-----------------------+-------------+
```

​    

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

```sql
SELECT e.nombre, e.apellido1,j.nombre AS nombreJefe, j.apellido1 AS apellidoJefe
FROM empleado as e
JOIN empleado as j ON e.id_jefe = j.id;

+----------+-----------+------------+--------------+
| nombre   | apellido1 | nombreJefe | apellidoJefe |
+----------+-----------+------------+--------------+
| Mary     | Patterson | Diane      | Murphy       |
| Jeff     | Firrelli  | Mary       | Patterson    |
| William  | Patterson | Diane      | Murphy       |
| Barry    | Jones     | Mary       | Patterson    |
| Andy     | Fixter    | Barry      | Jones        |
| Leslie   | Jennings  | Barry      | Jones        |
| Leslie   | Thompson  | Barry      | Jones        |
| Julie    | Firrelli  | Mary       | Patterson    |
| Steve    | Patterson | Julie      | Firrelli     |
| Foon Yue | Tseng     | Andy       | Fixter       |
| George   | Vanauf    | Andy       | Fixter       |
| Loui     | Bondur    | Barry      | Jones        |
| Gerard   | Hernandez | Loui       | Bondur       |
| Pamela   | Castillo  | Loui       | Bondur       |
| Larry    | Bott      | Barry      | Jones        |
| Barry    | Jones     | Larry      | Bott         |
| Andy     | Fixter    | Barry      | Jones        |
| Peter    | Marsh     | Barry      | Jones        |
+----------+-----------+------------+--------------+
```

​    

9. Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.

```sql
SELECT e.nombre, e.apellido1, j.nombre AS nombreJefe, j.apellido1 AS apellidoJefe, jj.nombre AS nombreJefeDelJefe, jj.apellido1 AS apellidoJefeDelJefe
FROM empleado as e
JOIN empleado as j ON e.id_jefe = j.id
JOIN empleado as jj ON j.id_jefe = jj.id;

+----------+-----------+------------+--------------+-------------------+---------------------+
| nombre   | apellido1 | nombreJefe | apellidoJefe | nombreJefeDelJefe | apellidoJefeDelJefe |
+----------+-----------+------------+--------------+-------------------+---------------------+
| Jeff     | Firrelli  | Mary       | Patterson    | Diane             | Murphy              |
| Barry    | Jones     | Mary       | Patterson    | Diane             | Murphy              |
| Andy     | Fixter    | Barry      | Jones        | Mary              | Patterson           |
| Leslie   | Jennings  | Barry      | Jones        | Mary              | Patterson           |
| Leslie   | Thompson  | Barry      | Jones        | Mary              | Patterson           |
| Julie    | Firrelli  | Mary       | Patterson    | Diane             | Murphy              |
| Steve    | Patterson | Julie      | Firrelli     | Mary              | Patterson           |
| Foon Yue | Tseng     | Andy       | Fixter       | Barry             | Jones               |
| George   | Vanauf    | Andy       | Fixter       | Barry             | Jones               |
| Loui     | Bondur    | Barry      | Jones        | Mary              | Patterson           |
| Gerard   | Hernandez | Loui       | Bondur       | Barry             | Jones               |
| Pamela   | Castillo  | Loui       | Bondur       | Barry             | Jones               |
| Larry    | Bott      | Barry      | Jones        | Mary              | Patterson           |
| Barry    | Jones     | Larry      | Bott         | Barry             | Jones               |
| Andy     | Fixter    | Barry      | Jones        | Mary              | Patterson           |
| Peter    | Marsh     | Barry      | Jones        | Mary              | Patterson           |
+----------+-----------+------------+--------------+-------------------+---------------------+
```

​    

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

```sql
SELECT c.nombre, c.apellido
FROM cliente AS c
JOIN pedido AS p ON c.id = p.id_cliente
JOIN estado AS e ON p.id_estado = e.id
WHERE e.nombre != 'Entregado' AND p.fecha_entrega > p.fecha_esperada;

+--------+----------+
| nombre | apellido |
+--------+----------+
| Maria  | Anders   |
| Yang   | Wang     |
| Susan  | Nelson   |
+--------+----------+
```

​    

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

```sql
SELECT  c.nombre, c.apellido, gp.gama AS GamaProducto
FROM cliente AS c
JOIN pedido AS pe ON c.id = pe.id_cliente
JOIN detalle_pedido AS dp ON pe.id = dp.id_pedido
JOIN producto AS p ON dp.id_producto = p.id
JOIN gama_producto as gp ON gp.id = p.id_gama;

+---------+------------+--------------+
| nombre  | apellido   | GamaProducto |
+---------+------------+--------------+
| Ernst   | Handel     | Alta         |
| Ernst   | Handel     | Alta         |
| Wilhelm | Bergulfsen | Alta         |
| Roland  | Mendel     | Alta         |
| Ernst   | Handel     | Baja         |
| Wilhelm | Bergulfsen | Baja         |
| Roland  | Mendel     | Baja         |
| Helen   | Bennett    | Baja         |
| Helen   | Bennett    | Media        |
| Maria   | Anders     | Media        |
| Maria   | Anders     | Media        |
+---------+------------+--------------+
```

​    

**Consultas multitabla (Composición externa)**

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

```sql
SELECT c.nombre, c.apellido
FROM cliente AS c
LEFT JOIN pago AS p ON c.id = p.id_cliente
WHERE p.id IS NULL;

Empty set
```

​    

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

```sql
SELECT c.nombre, c.apellido
FROM cliente AS c
LEFT JOIN pedido as p ON c.id = p.id_cliente
WHERE p.id IS NULL;

Empty set
```

​    

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

```sql
SELECT c.nombre, c.apellido
FROM cliente AS c
LEFT JOIN pedido as p ON c.id = p.id_cliente
LEFT JOIN pago as pa ON c.id = pa.id_cliente
WHERE p.id IS NULL AND pa.id IS NULL;

Empty set
```

​    

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

```sql
SELECT e.nombre, e.apellido1
FROM empleado AS e
LEFT JOIN oficina AS o ON e.id_oficina = o.id
WHERE o.id IS NULL;

Empty set
```

​    

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

```sql
SELECT e.nombre, e.apellido1
FROM empleado AS e
LEFT JOIN cliente AS c ON e.id = c.id_empleado_rep_ventas
WHERE c.id IS NULL;

+----------+-----------+
| nombre   | apellido1 |
+----------+-----------+
| Diane    | Murphy    |
| Mary     | Patterson |
| Jeff     | Firrelli  |
| Barry    | Jones     |
| Andy     | Fixter    |
| Leslie   | Thompson  |
| Foon Yue | Tseng     |
| George   | Vanauf    |
| Loui     | Bondur    |
+----------+-----------+
```

​    

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

```sql
SELECT e.nombre, e.apellido1, o.id AS cod_oficina, ciu.nombre, o.codigo_postal AS cod_postal
FROM empleado AS e
JOIN oficina AS o ON e.id_oficina = o.id
JOIN direccion_oficina AS dof ON dof.id_oficina = o.id
JOIN ciudad as ciu ON ciu.id = dof.id_ciudad
LEFT JOIN cliente AS c ON c.id_empleado_rep_ventas = e.id
WHERE c.id IS NULL;

+----------+-----------+-------------+-------------+------------+
| nombre   | apellido1 | cod_oficina | nombre      | cod_postal |
+----------+-----------+-------------+-------------+------------+
| Leslie   | Thompson  | 1           | Los Angeles | 90003      |
| Andy     | Fixter    | 1           | Los Angeles | 90003      |
| Barry    | Jones     | 1           | Los Angeles | 90003      |
| Jeff     | Firrelli  | 1           | Los Angeles | 90003      |
| Mary     | Patterson | 1           | Los Angeles | 90003      |
| Diane    | Murphy    | 1           | Los Angeles | 90003      |
| George   | Vanauf    | 3           | Guadalajara | 44670      |
| Foon Yue | Tseng     | 3           | Guadalajara | 44670      |
| Loui     | Bondur    | 4           | Los Angeles | 90003      |
+----------+-----------+-------------+-------------+------------+
```

​    

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado.

```sql
SELECT e.nombre, e.apellido1
FROM empleado AS e
LEFT JOIN oficina AS o ON e.id_oficina = o.id
LEFT JOIN cliente AS c ON c.id_empleado_rep_ventas = e.id
WHERE o.id IS NULL AND c.id IS NULL;

Empty set
```

​    

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

```sql
SELECT p.nombre
FROM producto AS p
LEFT JOIN detalle_pedido ON detalle_pedido.id_producto = p.id
WHERE detalle_pedido.id_producto IS NULL;

Empty set
```

​    

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto.

```sql
SELECT p.nombre, p.descripcion
FROM producto AS p
LEFT JOIN detalle_pedido ON detalle_pedido.id_producto = p.id
WHERE detalle_pedido.id_producto IS NULL;

Empty set
```

​    

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

```sql
SELECT DISTINCT o.id, o.codigo_postal
FROM oficina AS o
LEFT JOIN empleado AS e ON o.id = e.id_oficina
LEFT JOIN cliente AS c ON e.id = c.id_empleado_rep_ventas
LEFT JOIN pedido AS p ON c.id = p.id_cliente
LEFT JOIN detalle_pedido AS dp ON p.id = dp.id_pedido
LEFT JOIN producto AS pr ON dp.id_producto = pr.id
LEFT JOIN gama_producto AS gp ON pr.id_gama = gp.id AND gp.gama = 'Frutales'
WHERE gp.id IS NULL

 +----+---------------+
 | id | codigo_postal |
 +----+---------------+
 | 1  | 90003         |
 | 2  | H1J 1C3       |
 | 3  | 44670         |
 | 4  | 90003         |
 | 6  | H1J 1C3       |
 | 7  | 44670         |
 +----+---------------+
```

​    

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

```sql
SELECT c.nombre, c.apellido1
FROM cliente AS c
LEFT JOIN pedido AS p ON c.id = p.id_cliente
LEFT JOIN pago AS pa ON c.id = pa.id_cliente
WHERE p.id IS NOT NULL AND pa.id IS NULL;
```

​    

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

```sql
SELECT e.nombre, e.apellido1, j.nombre AS NombreJefe, j.apellido1 AS ApellidoJefe
FROM empleado AS e
LEFT JOIN cliente AS c ON e.id = c.id_empleado_rep_ventas
LEFT JOIN empleado AS j ON e.id_jefe = j.id
WHERE c.id_empleado_rep_ventas IS NULL;

+----------------+------------------+------------+--------------+
| NombreEmpleado | ApellidoEmpleado | NombreJefe | ApellidoJefe |
+----------------+------------------+------------+--------------+
| Diane          | Murphy           | NULL       | NULL         |
| Mary           | Patterson        | Diane      | Murphy       |
| Jeff           | Firrelli         | Mary       | Patterson    |
| William        | Patterson        | Diane      | Murphy       |
| Barry          | Jones            | Mary       | Patterson    |
| Andy           | Fixter           | Barry      | Jones        |
| Leslie         | Thompson         | Barry      | Jones        |
| Steve          | Patterson        | Julie      | Firrelli     |
| Foon Yue       | Tseng            | Andy       | Fixter       |
| George         | Vanauf           | Andy       | Fixter       |
| Loui           | Bondur           | Barry      | Jones        |
| Andy           | Fixter           | Barry      | Jones        |
| Peter          | Marsh            | Barry      | Jones        |
+----------------+------------------+------------+--------------+
```

​    

**Consultas resumen**

1. ¿Cuántos empleados hay en la compañía?

   ```sql
   SELECT COUNT(empleado.id) AS TotalEmpleados
   FROM empleado;
   
   +----------------+
   | TotalEmpleados |
   +----------------+
   |             19 |
   +----------------+
   ```

   ​    

2. ¿Cuántos clientes tiene cada país?

```sql
SELECT pais.nombre AS Pais, COUNT(cliente.id) AS TotalClientes
FROM cliente
JOIN direccion_Cliente ON cliente.id = direccion_Cliente.id_cliente
JOIN ciudad ON direccion_Cliente.id_ciudad = ciudad.id
JOIN region ON ciudad.id_region = region.id
JOIN pais ON region.id_pais = pais.id
GROUP BY pais.nombre;

+----------------+---------------+
| Pais           | TotalClientes |
+----------------+---------------+
| Estados Unidos |             4 |
| Canadá         |             3 |
| México         |             3 |
+----------------+---------------+
```

​    

3. ¿Cuál fue el pago medio en 2009?

```sql
SELECT AVG(total) AS Media
FROM pago
WHERE YEAR(fecha) = 2009;

+-------+
| Media |
+-------+
|  NULL |
+-------+
```

​    

4. ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma descendente por el número de pedidos.

```sql
SELECT estado.nombre, COUNT(estado.nombre) AS TotalPedidos
FROM pedido
JOIN estado ON pedido.id_estado = estado.id
GROUP BY estado.nombre
ORDER BY TotalPedidos DESC;

+------------+--------------+
| nombre     | TotalPedidos |
+------------+--------------+
| En proceso |            4 |
| Pendiente  |            3 |
| Entregado  |            3 | 
+------------+--------------+
```

​    

5. Calcula el precio de venta del producto más caro y más barato en una misma consulta.

```sql
SELECT max(producto.precio_venta) as MasCaro, min(producto.precio_venta) as MasBarato
FROM producto;

+---------+-----------+
| MasCaro | MasBarato |
+---------+-----------+
|   98.58 |     31.92 |
+---------+-----------+
```

​    

6. Calcula el número de clientes que tiene la empresa.

```sql
SELECT COUNT(cliente.id) AS TotalClientes
FROM cliente;

+---------------+
| TotalClientes |
+---------------+
|            10 |
+---------------+
```

​    

7. ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?

```sql
SELECT COUNT(DISTINCT cliente.id) AS clientesMadrid
FROM cliente
JOIN direccion_Cliente ON cliente.id = direccion_Cliente.id_cliente
JOIN ciudad ON direccion_Cliente.id_ciudad = ciudad.id
WHERE ciudad.nombre = 'Madrid';

+----------------+
| clientesMadrid |
+----------------+
|              0 |
+----------------+
```

​    

8. ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan por M?

```sql
SELECT COUNT(c.id) AS clientesCiudadPorM
FROM cliente AS c
JOIN direccion_Cliente AS dirCli ON dirCli.id_cliente = c.id
JOIN ciudad as ciu ON ciu.id = dirCli.id_ciudad
WHERE ciu.nombre LIKE 'M%';

+--------------------+
| clientesCiudadPorM |
+--------------------+
|                  3 |
+--------------------+
```

​    

9. Devuelve el nombre de los representantes de ventas y el número de clientes al que atiende cada uno.

```sql
SELECT e.nombre, e.apellido1, COUNT(c.id) AS NumeroClientes
FROM empleado AS e
LEFT JOIN cliente AS c ON e.id = c.id_empleado_rep_ventas
WHERE e.puesto = 'Vendedor'
GROUP BY e.id;

+----------+-----------+----------------+
| nombre   | apellido1 | NumeroClientes |
+----------+-----------+----------------+
| Andy     | Fixter    |              0 |
| Leslie   | Jennings  |              1 |
| Leslie   | Thompson  |              0 |
| Steve    | Patterson |              0 |
| Foon Yue | Tseng     |              0 |
| George   | Vanauf    |              0 |
| Gerard   | Hernandez |              1 |
| Pamela   | Castillo  |              1 |
| Barry    | Jones     |              1 |
| Peter    | Marsh     |              0 |
+----------+-----------+----------------+
```

​    

10. Calcula el número de clientes que no tiene asignado representante de ventas.

```sql
SELECT COUNT(cliente.id) AS NumeroClientesSinRepresentante
FROM cliente
WHERE id_empleado_rep_ventas IS NULL;

+--------------------------------+
| NumeroClientesSinRepresentante |
+--------------------------------+
|                              0 |
+--------------------------------+
```

​    

11. Calcula la fecha del primer y último pago realizado por cada uno de los clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente.

```sql
SELECT c.nombre,c.apellido,MIN(p.fecha) AS PrimerPago, MAX(p.fecha) AS UltimoPago
FROM cliente AS c
LEFT JOIN pago AS p ON c.id = p.id_cliente
GROUP BY c.id;

+-----------+------------+------------+------------+
| nombre    | apellido   | PrimerPago | UltimoPago |
+-----------+------------+------------+------------+
| Ernst     | Handel     | 2023-01-10 | 2023-01-10 |
| Wilhelm   | Bergulfsen | 2023-01-11 | 2023-01-11 |
| Roland    | Mendel     | 2023-01-12 | 2023-01-12 |
| Helen     | Bennett    | 2023-01-13 | 2023-01-13 |
| Maria     | Anders     | 2023-01-14 | 2023-01-14 |
| Francisco | Chang      | 2023-01-15 | 2023-01-15 |
| Yang      | Wang       | 2023-01-16 | 2023-01-16 |
| Pedro     | Afonso     | 2023-01-17 | 2023-01-17 |
| Anna      | Bedecs     | 2023-01-18 | 2023-01-18 |
| Susan     | Nelson     | 2023-01-19 | 2023-01-19 |
+-----------+------------+------------+------------+
```

​    

12. Calcula el número de productos diferentes que hay en cada uno de los pedidos.

```sql
SELECT id_pedido,COUNT(DISTINCT id_producto) AS NumProductosDiferentes
FROM detalle_pedido
GROUP BY id_pedido;

+-----------+------------------------+
| id_pedido | NumProductosDiferentes |
+-----------+------------------------+
|     10100 |                      2 |
|     10101 |                      2 |
|     10102 |                      2 |
|     10103 |                      2 |
|     10104 |                      2 |
+-----------+------------------------+
```

​    

13. Calcula la suma de la cantidad total de todos los productos que aparecen en cada uno de los pedidos.

```sql
SELECT id_pedido, SUM(cantidad) AS CantidadTotalProductos
FROM detalle_pedido
GROUP BY id_pedido;

+-----------+------------------------+
| id_pedido | CantidadTotalProductos |
+-----------+------------------------+
|     10100 |                      3 |
|     10101 |                      5 |
|     10102 |                      4 |
|     10103 |                      3 |
|     10104 |                      6 |
+-----------+------------------------+
```

​    

14. Devuelve un listado de los 20 productos más vendidos y el número total de unidades que se han vendido de cada uno. El listado deberá estar ordenado por el número total de unidades vendidas.

```sql
SELECT dp.id_producto,p.nombre,SUM(dp.cantidad) AS TotalVendidas
FROM detalle_pedido AS dp
JOIN producto AS p ON dp.id_producto = p.id
GROUP BY dp.id_producto, p.nombre
ORDER BY TotalVendidas DESC
LIMIT 20;

+-------------+---------------------------------------+---------------+
| id_producto | nombre                                | TotalVendidas |
+-------------+---------------------------------------+---------------+
| S10_1678    | 1969 Harley Davidson Ultimate Chopper |             7 |
| S12_4675    | 1969 Dodge Charger                    |             4 |
| S10_2016    | 1996 Moto Guzzi 1100i                 |             3 |
| S12_2823    | 2002 Suzuki XREO                      |             3 |
| S12_4473    | 1957 Chevy Pickup                     |             2 |
| S18_1129    | 1993 Mazda RX-7                       |             2 |
| S10_4698    | 2003 Harley-Davidson Eagle Drag Bike  |             2 |
| S10_1949    | 1952 Alpine Renault 1300              |             1 |
| S12_1099    | 1968 Ford Mustang                     |             1 |
| S12_3990    | 1970 Plymouth Hemi Cuda               |             1 |
+-------------+---------------------------------------+---------------+
```

​    

15. La facturación que ha tenido la empresa en toda la historia, indicando la base imponible, el IVA y el total facturado. La base imponible se calcula sumando el coste del producto por el número de unidades vendidas de la tabla detalle_pedido. El IVA es el 21 % de la base imponible, y el total la suma de los dos campos anteriores.

```sql
SELECT SUM(base_imponible) AS TotalBaseImponible, SUM(iva) AS TotalIVA, SUM(total_facturado) AS FacturacionTotal
FROM (
    SELECT SUM(precio_venta * cantidad) AS base_imponible, SUM(precio_venta * cantidad) * 0.21 AS iva, SUM(precio_venta * cantidad) * 1.21 AS total_facturado
    FROM detalle_pedido
    JOIN producto ON detalle_pedido.id_producto = producto.id
    GROUP BY id_pedido
) AS factura_total;

+--------------------+----------+------------------+
| TotalBaseImponible | TotalIVA | FacturacionTotal |
+--------------------+----------+------------------+
|            1668.67 | 350.4207 |        2019.0907 |
+--------------------+----------+------------------+
```

​    

16. La misma información que en la pregunta anterior, pero agrupada por código de producto.

```sql
SELECT SUM(base_imponible) AS TotalBaseImponible, SUM(iva) AS TotalIVA, SUM(total_facturado) AS FacturacionTotal
FROM (
    SELECT id_producto,SUM(precio_unidad * cantidad) AS base_imponible,SUM(precio_unidad * cantidad) * 0.21 AS iva,
SUM(precio_unidad * cantidad) * 1.21 AS total_facturado
    FROM detalle_pedido
    GROUP BY id_producto
) AS factura_total;

+--------------------+----------+------------------+
| TotalBaseImponible | TotalIVA | FacturacionTotal |
+--------------------+----------+------------------+
|            1479.57 | 310.7097 |        1790.2797 |
+--------------------+----------+------------------+
```

​    

17. La misma información que en la pregunta anterior, pero agrupada por código de producto filtrada por los códigos que empiecen por OR.

```sql
SELECT SUM(base_imponible) AS TotalBaseImponible, SUM(iva) AS TotalIVA, SUM(total_facturado) AS FacturacionTotal
FROM (
    SELECT id_producto,SUM(precio_unidad * cantidad) AS base_imponible,SUM(precio_unidad * cantidad) * 0.21 AS iva,
SUM(precio_unidad * cantidad) * 1.21 AS total_facturado
    FROM detalle_pedido
    JOIN producto ON detalle_pedido.id_producto = producto.id
    WHERE producto.id LIKE 'OR%'
    GROUP BY id_producto
) AS factura_total;

+--------------------+----------+------------------+
| TotalBaseImponible | TotalIVA | FacturacionTotal |
+--------------------+----------+------------------+
|               NULL |     NULL |             NULL |
+--------------------+----------+------------------+
```

​    

18. Lista las ventas totales de los productos que hayan facturado más de 3000 euros. Se mostrará el nombre, unidades vendidas, total facturado y total facturado con impuestos (21% IVA).

```sql
SELECT p.nombre,SUM(dp.cantidad) AS UnidadesVendidas,SUM(dp.precio_unidad * dp.cantidad) AS TotalFacturado, SUM(dp.precio_unidad* dp.cantidad) * 1.21 AS TotalFacturadoConIVA
FROM detalle_pedido AS dp
JOIN producto AS p ON dp.id_producto = p.id
GROUP BY p.nombre
HAVING TotalFacturado > 3000;

Empty set
```

​    

19. Muestre la suma total de todos los pagos que se realizaron para cada uno de los años que aparecen en la tabla pagos.

```sql
SELECT YEAR(fecha) AS Año, SUM(total) AS TotalPagado
FROM pago
GROUP BY YEAR(fecha);

+------+-------------+
| Año  | TotalPagado |
+------+-------------+
| 2023 |    13320.77 |
+------+-------------+
```

​    

**Subconsultas**

Con operadores básicos de comparación

1. Devuelve el nombre del cliente con mayor límite de crédito.

   ```sql
      SELECT nombre, apellido
      FROM cliente
      WHERE limite_credito = (
          SELECT MAX(limite_credito)
          FROM cliente
      );
   
   +--------+----------+
   | nombre | apellido |
   +--------+----------+
   | Anna   | Bedecs   |
   +--------+----------+
   ```

   

2. Devuelve el nombre del producto que tenga el precio de venta más caro.

   ```sql
    SELECT nombre 
      FROM producto
      WHERE precio_venta = (
          SELECT MAX(precio_venta)
          FROM producto
      );
   
   +--------------------------+
   | nombre                   |
   +--------------------------+
   | 1952 Alpine Renault 1300 |
   +--------------------------+
   ```

   

3. Devuelve el nombre del producto del que se han vendido má s unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de
    unidades que se han vendido de cada producto a partir de los datos de la
    tabla detalle_pedido)

  ```sql
   SELECT nombre
     FROM producto
     WHERE id = (
         SELECT id_producto
         FROM detalle_pedido
         GROUP BY id_producto
         ORDER BY SUM(cantidad) DESC
         LIMIT 1
     );
     
  +--------------------+
  | nombre             |
  +--------------------+
  | 1969 Dodge Charger |
  +--------------------+
  ```

  

4. Los clientes cuyo límite de crédito sea mayor que los pagos que haya
    realizado. (Sin utilizar INNER JOIN).

  ```sql
  SELECT nombre,apellido
  FROM cliente
  WHERE limite_credito > (
  	SELECT SUM(total) 
      FROM pago
      WHERE id_cliente = cliente.id
  );
  
  +---------+------------+
  | nombre  | apellido   |
  +---------+------------+
  | Ernst   | Handel     |
  | Wilhelm | Bergulfsen |
  +---------+------------+
  ```

  

5. Devuelve el producto que más unidades tiene en stock.

   ```sql
   SELECT nombre
   FROM producto
   WHERE cantidad_en_stock = (
   	SELECT MAX(cantidad_en_stock)
   	FROM producto
   );
   
   +------------------+
   | nombre           |
   +------------------+
   | 2002 Suzuki XREO |
   +------------------+
   ```

   

6. Devuelve el producto que menos unidades tiene en stock.

   ```sql
   SELECT nombre
   FROM producto
   WHERE cantidad_en_stock = (
   	SELECT MIN(cantidad_en_stock)
   	FROM producto
   );
   
   +-------------------+
   | nombre            |
   +-------------------+
   | 1968 Ford Mustang |
   +-------------------+
   ```

   

7. Devuelve el nombre, los apellidos y el email de los empleados que están a
    cargo de Alberto Soria.

  ```sql
  SELECT nombre, apellido1, apellido2
  FROM empleado
  WHERE id_jefe = (
  	SELECT id
      FROM empleado
      WHERE nombre = 'Alberto' AND apellido1 = 'Soria'	
  );
  
  Empty set (0.00 sec)
  ```


  **Subconsultas con ALL y ANY**

8. Devuelve el nombre del cliente con mayor límite de crédito.

   ```sql
   SELECT nombre, apellido
   FROM cliente
   WHERE limite_credito >= ALL (
   	SELECT limite_credito
       FROM cliente
   );
   
   +--------+----------+
   | nombre | apellido |
   +--------+----------+
   | Anna   | Bedecs   |
   +--------+----------+
   ```

   

9. Devuelve el nombre del producto que tenga el precio de venta más caro.

   ```sql
   SELECT nombre
   FROM producto
   WHERE precio_venta >= ALL (
   	SELECT precio_venta
       FROM producto
   );
   
   +--------------------------+
   | nombre                   |
   +--------------------------+
   | 1952 Alpine Renault 1300 |
   +--------------------------+
   ```

   

10. Devuelve el producto que menos unidades tiene en stock.

    ```sql
    SELECT nombre
    FROM producto
    WHERE cantidad_en_stock <= ALL (
    	SELECT MIN(cantidad_en_stock)
    	FROM producto
    );
    
    +-------------------+
    | nombre            |
    +-------------------+
    | 1968 Ford Mustang |
    +-------------------+
    ```

    **Subconsultas con IN y NOT IN**

    

11. Devuelve el nombre, apellido1 y cargo de los empleados que no
    representen a ningún cliente.

    ```sql
    SELECT nombre, apellido1, puesto
    FROM empleado
    WHERE id NOT IN (
    	SELECT id_empleado_rep_ventas
        FROM cliente
    );
    
    +----------+-----------+--------------------------+
    | nombre   | apellido1 | puesto                   |
    +----------+-----------+--------------------------+
    | Diane    | Murphy    | Presidente               |
    | Mary     | Patterson | Vicepresidente de ventas |
    | Jeff     | Firrelli  | Gerente de ventas        |
    | Barry    | Jones     | Gerente de ventas (NA)   |
    | Andy     | Fixter    | Vendedor                 |
    | Leslie   | Thompson  | Vendedor                 |
    | Foon Yue | Tseng     | Vendedor                 |
    | George   | Vanauf    | Vendedor                 |
    | Loui     | Bondur    | Gerente de ventas (EMEA) |
    +----------+-----------+--------------------------+
    ```
    
    
    
12. Devuelve un listado que muestre solamente los clientes que no han
    realizado ningún pago.

    ```sql
    SELECT nombre, apellido
    FROM cliente
    WHERE id NOT IN (
    	SELECT id_cliente
        FROM pago
    );
    
    Empty set (0.00 sec)
    ```

    
    
13. Devuelve un listado que muestre solamente los clientes que sí han realizado
    algún pago.

    ```sql
    SELECT nombre, apellido
    FROM cliente
    WHERE id IN (
    	SELECT id_cliente
        FROM pago
    );
    
    +-----------+------------+
    | nombre    | apellido   |
    +-----------+------------+
    | Ernst     | Handel     |
    | Wilhelm   | Bergulfsen |
    | Roland    | Mendel     |
    | Helen     | Bennett    |
    | Maria     | Anders     |
    | Francisco | Chang      |
    | Yang      | Wang       |
    | Pedro     | Afonso     |
    | Anna      | Bedecs     |
    | Susan     | Nelson     |
    +-----------+------------+
    ```
    
    
    
14. Devuelve un listado de los productos que nunca han aparecido en un
    pedido.

    ```sql
    SELECT nombre 
    FROM producto
    WHERE id NOT IN (
    	SELECT id_producto 
    	FROM detalle_pedido
    );
    
	Empty set
    ```
    
    
    
15. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos
    empleados que no sean representante de ventas de ningún cliente.

    ```sql
    SELECT e.nombre, e.apellido1, e.apellido2, t_o.telefono AS tel_oficina
    FROM empleado as e
    INNER JOIN oficina as o ON e.id_oficina = o.id
    INNER JOIN telefono_oficina as t_o ON t_o.id_oficina = o.id
    WHERE e.id NOT IN (
    	SELECT id_empleado_rep_ventas 
    	FROM cliente
    );
    
    +----------+-----------+-----------+--------------+
    | nombre   | apellido1 | apellido2 | tel_oficina  |
    +----------+-----------+-----------+--------------+
    | Diane    | Murphy    |           | 555-555-0100 |
    | Mary     | Patterson |           | 555-555-0100 |
    | Jeff     | Firrelli  |           | 555-555-0100 |
    | Barry    | Jones     |           | 555-555-0100 |
    | Andy     | Fixter    |           | 555-555-0100 |
    | Leslie   | Thompson  |           | 555-555-0100 |
    | Foon Yue | Tseng     |           | 555-555-0102 |
    | George   | Vanauf    |           | 555-555-0102 |
    | Loui     | Bondur    |           | 555-555-0100 |
    +----------+-----------+-----------+--------------+
    ```
    
    
    
16. Devuelve las oficinas donde no trabajan ninguno de los empleados que
    hayan sido los representantes de ventas de algún cliente que haya realizado
    la compra de algún producto de la gama Frutales.

    ```sql
    SELECT id, codigo_postal
    FROM oficina
    WHERE id NOT IN (
    SELECT DISTINCT id_oficina
    FROM empleado
    WHERE id IN (
        SELECT DISTINCT id_empleado_rep_ventas
        FROM cliente
        WHERE id IN (
            SELECT DISTINCT id_cliente 
            FROM pedido
            WHERE id IN (
                SELECT DISTINCT id_pedido
                FROM detalle_pedido
                WHERE id_producto IN (
                    SELECT id 
                    FROM producto
                    WHERE id_gama = (
                        SELECT id
                        FROM gama_producto
                        WHERE gama = 'Frutales'
                    )
                )
            )
        )
    )
    );

    +----+---------------+
    | id | codigo_postal |
    +----+---------------+
    | 1  | 90003         |
    | 2  | H1J 1C3       |
    | 3  | 44670         |
    | 4  | 90003         |
    | 6  | H1J 1C3       |
    | 7  | 44670         |
    +----+---------------+
    ```
        
17. Devuelve un listado con los clientes que han realizado algún pedido pero no
    han realizado ningún pago.

    ```sql
    SELECT nombre, apellido
    FROM cliente
    WHERE id IN (
    	SELECT id_cliente
    	FROM pedido
    )
    AND id NOT IN (
    	SELECT id_cliente
    	FROM pago
    );
    
    Empty set (0.00 sec)
    ```

**Subconsultas con EXISTS y NOT EXISTS**

18. Devuelve un listado que muestre solamente los clientes que no han
    realizado ningún pago.

    ```sql
    SELECT nombre, apellido
    FROM cliente c
    WHERE NOT EXISTS (
        SELECT 1
        FROM pago p
        WHERE p.id_cliente = c.id
    );
    Empty set (0.00 sec)
    ```

    
    
19. Devuelve un listado que muestre solamente los clientes que sí han realizado
    algún pago.

    ```sql
    SELECT nombre, apellido
    FROM cliente
    WHERE EXISTS (
    	SELECT 1
    	FROM pago
        WHERE pago.id_cliente = cliente.id
    );
    
    +-----------+------------+
    | nombre    | apellido   |
    +-----------+------------+
    | Ernst     | Handel     |
    | Wilhelm   | Bergulfsen |
    | Roland    | Mendel     |
    | Helen     | Bennett    |
    | Maria     | Anders     |
    | Francisco | Chang      |
    | Yang      | Wang       |
    | Pedro     | Afonso     |
    | Anna      | Bedecs     |
    | Susan     | Nelson     |
    +-----------+------------+
    ```
    
    
    
20. Devuelve un listado de los productos que nunca han aparecido en un
    pedido.

    ```sql
    SELECT nombre
    FROM producto
    WHERE NOT EXISTS (
    	SELECT 1
    	FROM detalle_pedido
        WHERE producto.id = detalle_pedido.id_producto
    );
    
    Empty set (0.00 sec)
    ```
    
    
    
21. Devuelve un listado de los productos que han aparecido en un pedido
    alguna vez.

    ```sql
    SELECT nombre
    FROM producto
    WHERE EXISTS (
    	SELECT 1
    	FROM detalle_pedido
        WHERE producto.id = detalle_pedido.id_producto
    );
    
    +---------------------------------------+
    | nombre                                |
    +---------------------------------------+
    | 1952 Alpine Renault 1300              |
    | 1957 Chevy Pickup                     |
    | 1968 Ford Mustang                     |
    | 1969 Dodge Charger                    |
    | 1969 Harley Davidson Ultimate Chopper |
    | 1970 Plymouth Hemi Cuda               |
    | 1993 Mazda RX-7                       |
    | 1996 Moto Guzzi 1100i                 |
    | 2002 Suzuki XREO                      |
    | 2003 Harley-Davidson Eagle Drag Bike  |
    +---------------------------------------+
    ```
    
    

**Consultas variadas**

1. Devuelve el listado de clientes indicando el nombre del cliente y cuántos pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no han realizado ningún pedido.

```sql
SELECT c.nombre AS NombreCliente, COUNT(p.id) AS PedidosRealizados
FROM cliente AS c
LEFT JOIN pedido AS p ON c.id = p.id_cliente
GROUP BY c.nombre;

+---------------+-------------------+
| NombreCliente | PedidosRealizados |
+---------------+-------------------+
| Ernst         |                 1 |
| Wilhelm       |                 1 |
| Roland        |                 1 |
| Helen         |                 1 |
| Maria         |                 1 |
| Francisco     |                 1 |
| Yang          |                 1 |
| Pedro         |                 1 |
| Anna          |                 1 |
| Susan         |                 1 |
+---------------+-------------------+
```

​    

2. Devuelve un listado con los nombres de los clientes y el total pagado por cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han realizado ningún pago.

```sql
SELECT c.nombre, c.apellido, SUM(p.total) total_pagado
FROM cliente AS c
LEFT JOIN pago p ON p.id_cliente = cliente.id;
GROUP BY c.nombre, c.apellido;

+-----------+------------+--------------+
| nombre    | apellido   | total_pagado |
+-----------+------------+--------------+
| Ernst     | Handel     |       488.10 |
| Wilhelm   | Bergulfsen |      1064.67 |
| Roland    | Mendel     |      1380.00 |
| Helen     | Bennett    |      1165.00 |
| Maria     | Anders     |      1401.00 |
| Francisco | Chang      |      1621.00 |
| Yang      | Wang       |      1501.00 |
| Pedro     | Afonso     |      1400.00 |
| Anna      | Bedecs     |      1800.00 |
| Susan     | Nelson     |      1500.00 |
+-----------+------------+--------------+
```

​    

3. Devuelve el nombre de los clientes que hayan hecho pedidos en 2008 ordenados alfabéticamente de menor a mayor.

```sql
SELECT c.nombre AS NombreCliente
FROM cliente AS c
JOIN pedido AS p ON c.id = p.id_cliente
WHERE YEAR(p.fecha_pedido) = 2008
ORDER BY c.nombre ASC;

Empty set
```

​    

4. Devuelve el nombre del cliente, el nombre y primer apellido de su representante de ventas y el número de teléfono de la oficina del  representante de ventas, de aquellos clientes que no hayan realizado  ningún pago.

```sql
SELECT c.nombre, c.apellido, e.nombre as NombreRepresentante, e.apellido1 as ApellidoRepresentante, telOfi.telefono AS Telefono_Oficina
FROM cliente AS c
LEFT JOIN pago AS p ON c.id = p.id_cliente
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id
JOIN oficina AS o ON e.id_oficina = o.id
JOIN telefono_oficina AS telOfi ON o.id = telOfi.id_oficina
WHERE p.id IS NULL;

Empty set
```

​    

5. Devuelve el listado de clientes donde aparezca el nombre del cliente, el nombre y primer apellido de su representante de ventas y la ciudad donde está su oficina.

```sql
SELECT c.nombre, c.apellido, e.nombre as NombreRepresentante, e.apellido1, ciu.nombre
FROM cliente as c
JOIN empleado as e ON e.id = c.id_empleado_rep_ventas
JOIN oficina as o ON o.id = e.id_oficina
JOIN direccion_oficina as dirOfi ON dirOfi.id_oficina = o.id
JOIN ciudad as ciu ON ciu.id = dirOfi.id_ciudad;

+---------+------------+---------------------+-----------+-------------+
| nombre  | apellido   | NombreRepresentante | apellido1 | nombre      |
+---------+------------+---------------------+-----------+-------------+
| Ernst   | Handel     | Gerard              | Hernandez | Los Angeles |
| Wilhelm | Bergulfsen | Barry               | Jones     | Guadalajara |
| Roland  | Mendel     | Julie               | Firrelli  | Montreal    |
| Helen   | Bennett    | Leslie              | Jennings  | Los Angeles |
| Maria   | Anders     | Pamela              | Castillo  | Los Angeles |
| Yang    | Wang       | Larry               | Bott      | Guadalajara |
+---------+------------+---------------------+-----------+-------------+
```

​    

6. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

```sql
SELECT e.nombre as NombreRepresentante, e.apellido1, telOfi.telefono AS Telefono_Oficina
FROM empleado AS e
LEFT JOIN cliente AS c ON e.id = c.id_empleado_rep_ventas
JOIN oficina AS o ON e.id_oficina = o.id
JOIN telefono_oficina AS telOfi ON o.id = telOfi.id_oficina
WHERE c.id_empleado_rep_ventas IS NULL;

+---------------------+-----------+------------------+
| NombreRepresentante | apellido1 | Telefono_Oficina |
+---------------------+-----------+------------------+
| Diane               | Murphy    | 555-555-0100     |
| Mary                | Patterson | 555-555-0100     |
| Jeff                | Firrelli  | 555-555-0100     |
| William             | Patterson | 555-555-0101     |
| Barry               | Jones     | 555-555-0100     |
| Andy                | Fixter    | 555-555-0100     |
| Leslie              | Thompson  | 555-555-0100     |
| Steve               | Patterson | 555-555-0101     |
| Foon Yue            | Tseng     | 555-555-0102     |
| George              | Vanauf    | 555-555-0102     |
| Loui                | Bondur    | 555-555-0100     |
+---------------------+-----------+------------------+
```

​    

7. Devuelve un listado indicando todas las ciudades donde hay oficinas y el número de empleados que tiene.

```sql
SELECT ciu.nombre, COUNT(e.id) AS NumeroEmpleados
FROM empleado AS e
JOIN oficina AS o ON e.id_oficina = o.id
JOIN ciudad AS ciu ON o.id_ciudad = ciu.id
GROUP BY ciu.nombre;

+-------------+-----------------+
| Ciudad      | NumeroEmpleados |
+-------------+-----------------+
| Los Angeles |              10 |
| Montreal    |               3 |
| Guadalajara |               4 |
+-------------+-----------------+
```



**VISTAS**

1.

```sql
CREATE VIEW rechazados_2009 AS 
SELECT p.id, p.fecha_pedido, e.nombre, p.comentarios
FROM pedido as p, estado as e
WHERE p.id_estado = e.id AND YEAR(p.fecha_pedido) = 2009;

SELECT id, fecha_pedido, nombre, comentarios
FROM rechazados_2009;
```

2.

```sql
CREATE VIEW pedidos_entregados_enero_cualquier_anio AS
SELECT p.id, p.fecha_pedido, e.nombre, p.comentarios
FROM pedido as p, estado as e
WHERE p.id_estado = e.id AND DATE_FORMAT(fecha_entrega, '%m') = '01' AND p.id_estado = 3;

SELECT id, fecha_pedido, nombre, comentarios
FROM pedidos_entregados_enero_cualquier_anio;
```

3. 

```sql
 CREATE VIEW total_pedidos_cada_estado AS
 SELECT estado.nombre, COUNT(estado.nombre) AS TotalPedidos
 FROM pedido
 JOIN estado ON pedido.id_estado = estado.id
 GROUP BY estado.nombre
 ORDER BY TotalPedidos DESC;
 
 SELECT nombre,TotalPedidos
 FROM total_pedidos_cada_estado;

```

4. 

```sql
CREATE VIEW cliente_y_representante AS
SELECT c.id, c.nombre, c.apellido, e.nombre AS NombreRepresentante, e.apellido1 AS ApellidoRepresentante
FROM cliente AS c
JOIN empleado AS e ON c.id_empleado_rep_ventas = e.id;

SELECT nombre, apellido, NombreRepresentante, ApellidoRepresentante
FROM cliente_y_representante;
```

5. 

```sql
CREATE VIEW tipo_de_pago AS
SELECT id, nombre
FROM tipo_pago;

SELECT id,nombre
FROM tipo_pago;
```

6. 

```sql
CREATE VIEW oficinas AS
SELECT ofi.id, ciu.nombre 
FROM oficina AS ofi
INNER JOIN direccion_oficina do ON do.id_oficina = ofi.id
INNER JOIN ciudad ciu ON ciu.id = do.id_ciudad;

SELECT id, nombre
FROM oficinas;
```

7.

```sql
CREATE VIEW pedidos_retrasados AS
SELECT id, id_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE fecha_entrega > fecha_esperada;

SELECT id, id_cliente, fecha_esperada, fecha_entrega
FROM pedidos_retrasados;
```

8.

```sql
CREATE VIEW cantidad_pedidos_por_cliente AS
SELECT c.nombre AS NombreCliente, COUNT(p.id) AS PedidosRealizados
FROM cliente AS c
LEFT JOIN pedido AS p ON c.id = p.id_cliente
GROUP BY c.nombre;

SELECT NombreCliente, PedidosRealizados
FROM cantidad_pedidos_por_cliente;
```

9.

```sql
CREATE VIEW total_pagado_cliente AS
SELECT c.nombre, c.apellido, SUM(p.total) AS total_pagado
FROM cliente AS c
LEFT JOIN pago p ON p.id_cliente = c.id
GROUP BY c.nombre, c.apellido;

SELECT nombre, apellido, total_pagado
FROM total_pagado_cliente;
```

10. 

```sql
CREATE VIEW empleado_y_jefe AS
SELECT e.nombre, e.apellido1,j.nombre AS nombreJefe, j.apellido1 AS apellidoJefe
FROM empleado as e
JOIN empleado as j ON e.id_jefe = j.id;

SELECT nombre, apellido1, nombreJefe, apellidoJefe
FROM empleado_y_jefe;
```



**Procedimientos almacenados**

1. Crear contacto

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS crear_contacto $$
   CREATE PROCEDURE crear_contacto(
   IN c_id INT,
   IN c_nombre VARCHAR(50),
   IN c_apellido VARCHAR(50),
   IN c_telefono VARCHAR (15),
   IN c_email VARCHAR (30),
   IN  c_id_cliente INT
   )
   BEGIN
   INSERT INTO contacto VALUES (c_id,c_nombre,c_apellido,c_telefono,c_email,c_id_cliente);
   END $$
   
   DELIMITER ;
   
   CALL crear_contacto (11,'Mario','Lopez','3121234567','mario66@gmail.com',1);
   ```
   
2. Actualizar contacto

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS actualizar_contacto $$
   CREATE PROCEDURE actualizar_contacto(
       IN c_id INT,
       IN c_nombre VARCHAR(50),
       IN c_apellido VARCHAR(50),
       IN c_telefono VARCHAR(15),
       IN c_email VARCHAR(30),
       IN c_id_cliente INT
   )
   BEGIN
       UPDATE contacto 
       SET 
           nombre = c_nombre,
           apellido = c_apellido,
           telefono = c_telefono,
           email = c_email,
           id_cliente = c_id_cliente
       WHERE id = c_id;
   END $$
   DELIMITER ;
   
   CALL actualizar_contacto (11,'Mario','Lopez','3121234567','mario666@gmail.com',1);
   ```

   

3. Eliminar contacto

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS eliminar_contacto $$
   CREATE PROCEDURE eliminar_contacto(
       IN c_id INT
   )
   BEGIN
       DELETE FROM contacto
       WHERE id = c_id;
   END $$
   DELIMITER ;
   
   CALL eliminar_contacto (11);
   ```

   

4. Seleccionar contacto

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS seleccionar_contacto $$
   CREATE PROCEDURE seleccionar_contacto(
       IN c_id INT
   )
   BEGIN
       SELECT * FROM contacto
       WHERE id = c_id;
   END $$
   DELIMITER ;
   
   CALL seleccionar_contacto (10);
   ```

   

5. crear tipo_telefono

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS crear_tipo_telefono $$
   CREATE PROCEDURE crear_tipo_telefono(
       IN t_tel_id INT,
       IN t_tel_nombre VARCHAR(20)
   )
   BEGIN
       INSERT INTO tipo_telefono VALUES (t_tel_id, t_tel_nombre);
   END $$
   DELIMITER ;
   ```

   

6. Actualizar tipo_telefono

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS actualizar_tipo_telefono $$
   CREATE PROCEDURE actualizar_tipo_telefono(
       IN t_tel_id INT,
       IN t_tel_nombre VARCHAR(20)
   )
   BEGIN
       UPDATE tipo_telefono
       SET nombre = t_tel_nombre
       WHERE id = t_tel_id;
   END $$
   DELIMITER ;
   
   CALL actualizar_tipo_telefono (1,'Esposo');
   ```

   

7. crear ciudad

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS crear_ciudad $$
   CREATE PROCEDURE crear_ciudad(
       IN ciu_id VARCHAR(5),
       IN ciu_nombre VARCHAR(50),
       IN ciu_id_region VARCHAR(2)
   )
   BEGIN
       INSERT INTO ciudad VALUES (ciu_id, ciu_nombre, ciu_id_region);
   END $$
   DELIMITER ;
   ```

   

8. Actualizar ciudad

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS actualizar_ciudad $$
   CREATE PROCEDURE actualizar_ciudad(
       IN ciu_id VARCHAR(5),
       IN ciu_nombre VARCHAR(50),
       IN ciu_id_region VARCHAR(2)
   )
   BEGIN
       UPDATE ciudad
       SET nombre = ciu_nombre,
           id_region = ciu_id_region
       WHERE id = ciu_id;
   END $$
   DELIMITER ;
   ```

   

9. Eliminar ciudad;

   ```sql
   DELIMITER $$
   DROP PROCEDURE IF EXISTS eliminar_ciudad $$
   CREATE PROCEDURE eliminar_ciudad(
       IN ciu_id VARCHAR(5)
   )
   BEGIN
       DELETE FROM ciudad
       WHERE id = ciu_id;
   END $$
   DELIMITER ;
   ```

   

10. Pedidos por estado

    ```sql
    DELIMITER $$
    DROP PROCEDURE IF EXISTS buscar_ciudad $$
    CREATE PROCEDURE buscar_ciudad(
        IN ciu_id VARCHAR(5)
    )
    BEGIN
        SELECT * FROM ciudad
        WHERE id = ciu_id;
    END $$
    DELIMITER ;
    ```
