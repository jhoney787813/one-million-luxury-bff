![image](https://github.com/user-attachments/assets/13cd7a94-1aa9-4279-af9d-902d0f29ef5c)

# Documentación de Diseño de Base de Datos

## Introducción

Este documento describe la estructura de la base de datos para el sistema de gestión de propiedades, propietarios e imágenes. Se utiliza PostgreSQL para gestionar las tablas relacionadas con las propiedades, propietarios y las imágenes asociadas. El modelo está diseñado para ser escalable, mantener la integridad referencial y permitir un fácil acceso a los datos relacionados.

## Estructura de la Base de Datos

### 1. Tabla **Image**
La tabla `Image` almacena información sobre las imágenes asociadas a las propiedades.

```sql
CREATE TABLE public."Image" (
    "IdImage" int8 GENERATED BY DEFAULT AS IDENTITY( INCREMENT BY 1 MINVALUE 1 MAXVALUE 9223372036854775807 START 1 CACHE 1 NO CYCLE) NOT NULL,
    "FileImage" text NULL,
    "Enabled" bool DEFAULT true NULL,
    "Created_at" timestamptz DEFAULT now() NOT NULL,
    CONSTRAINT "Image_IdPropertyImage_key" UNIQUE ("IdImage"),
    CONSTRAINT "Image_pkey" PRIMARY KEY ("IdImage")
);
```

**Justificación:**

*IdImage:* Identificador único de la imagen.

*FileImage:* URL o ruta del archivo de imagen.

*Enabled:* Indicador de si la imagen está habilitada.

*Created_at:* Marca temporal de creación.



### 2. Tabla **Owner**
La tabla Owner almacena la información de los propietarios de las propiedades.

```sql
CREATE TABLE public."Owner" (
    "IdOwner" int8 GENERATED BY DEFAULT AS IDENTITY( INCREMENT BY 1 MINVALUE 1 MAXVALUE 9223372036854775807 START 1 CACHE 1 NO CYCLE) NOT NULL,
    "Name" varchar NULL,
    "Address" text NULL,
    "Photo" varchar NULL,
    "Created_at" timestamptz DEFAULT now() NOT NULL,
    "Birthday" date NULL,
    CONSTRAINT "Owner_IdOwner_key" UNIQUE ("IdOwner"),
    CONSTRAINT "Owner_pkey" PRIMARY KEY ("IdOwner")
);
```

**Justificación:**

IdOwner: Identificador único del propietario.

*Name:* Nombre del propietario.

*Address:* Dirección del propietario.

*Photo:* Foto del propietario.

*Created_at:* Fecha de creación del registro.

*Birthday:* Fecha de nacimiento del propietario.


###  3. **Tabla Property**
La tabla Property contiene información sobre las propiedades, como el nombre, la dirección, el precio y el propietario asociado.

```sql
CREATE TABLE public."Property" (
    "IdProperty" int8 GENERATED BY DEFAULT AS IDENTITY( INCREMENT BY 1 MINVALUE 1 MAXVALUE 9223372036854775807 START 1 CACHE 1 NO CYCLE) NOT NULL,
    "Name" varchar NULL,
    "Address" varchar NULL,
    "Price" numeric NULL,
    "Created_at" timestamptz DEFAULT now() NOT NULL,
    "CodeInternal" varchar NULL,
    "Year" int2 NULL,
    "IdOwner" int8 NOT NULL,
    CONSTRAINT "Property_IdProperty_key" UNIQUE ("IdProperty")
);
```

**Justificación:**

*IdProperty:* Identificador único de la propiedad.

*Name:* Nombre de la propiedad.

*Address:* Dirección de la propiedad.

*Price:* Precio de la propiedad.

*Created_at:* Fecha de creación.

*CodeInternal:* Código interno para identificar la propiedad.

*Year:* Año de construcción.

*IdOwner:* Llave foránea que referencia a la tabla Owner.

### 4. Tabla **PropertyImage**
La tabla PropertyImage asocia las imágenes con las propiedades.
```sql
CREATE TABLE public."PropertyImage" (
    "IdPropertyImage" int8 GENERATED BY DEFAULT AS IDENTITY( INCREMENT BY 1 MINVALUE 1 MAXVALUE 9223372036854775807 START 1 CACHE 1 NO CYCLE) NOT NULL,
    "IdProperty" int8 NOT NULL,
    "Created_at" timestamptz DEFAULT now() NOT NULL,
    CONSTRAINT "PropertyImage_pkey" PRIMARY KEY ("IdPropertyImage", "IdProperty")
);
```


**Justificación:**

*IdPropertyImage:* Identificador único de la relación entre propiedad e imagen.

*IdProperty:* Llave foránea que referencia la propiedad asociada.

*Created_at:* Marca temporal de creación.


###5. **Tabla PropertyTrace**
La tabla PropertyTrace registra el historial de ventas de las propiedades.
```sql
CREATE TABLE public."PropertyTrace" (
    "IdPropertyTrace" int8 GENERATED BY DEFAULT AS IDENTITY( INCREMENT BY 1 MINVALUE 1 MAXVALUE 9223372036854775807 START 1 CACHE 1 NO CYCLE) NOT NULL,
    "DateSale" date NULL,
    "Name" varchar NULL,
    "Value" numeric NULL,
    "Tax" float8 NULL,
    "IdProperty" int8 NULL,
    "Created_at" timestamptz DEFAULT now() NOT NULL,
    CONSTRAINT "PropertyTrace_pkey" PRIMARY KEY ("IdPropertyTrace")
);
```

**Justificación:**

*IdPropertyTrace:* Identificador único para el historial de ventas.

*DateSale:* Fecha de la venta.

*Name:* Nombre de la transacción de venta.

*Value:* Valor de la venta.

*Tax:* Impuesto aplicado en la venta.

IdProperty: Llave foránea que referencia la propiedad.

## Relación entre las Tablas ##

La tabla "*Property*" tiene una relación con la tabla "*Owner*" a través de la columna "*IdOwner*".
La tabla "*PropertyImage*" está relacionada con "*Image*" y *Property* mediante las claves foráneas "*IdPropertyImage*" e "*IdProperty*".
La tabla "*PropertyTrace*" está relacionada con "*Property*" a través de la columna "*IdProperty*".

### BD PROPUESTA CON BASE EN EL MODEO ORIGINAL ###

![image](https://github.com/jhoney787813/one-million-luxury-app/blob/main/modeloBDnew.png)

# Documentación de Datos de Ejemplo

## Insertar Datos en la Tabla `Image`

```sql
INSERT INTO public."Image" ("FileImage", "Enabled", "Created_at") VALUES
('https://photos.zillowstatic.com/fp/c203408f4a7299b981129e46430b1e42-cc_ft_768.webp', true, now()),
('https://pic.le-cdn.com/thumbs/520x390/08/1/properties/Property-06308b4c34745cad51cc9ebd32c72d2d-130873476.jpg', true, now()),
('https://photos.zillowstatic.com/fp/556eb4f3406bf1c9db73fbacf3c32cfc-cc_ft_768.webp', false, now());
```

##  Insertar Datos en la Tabla Owner
```sql
INSERT INTO public."Owner" ("Name", "Address", "Photo", "Created_at", "Birthday") VALUES
('John Doe', '123 Main St, Springfield', 'owner1.jpg', now(), '1985-06-15'),
('Jane Smith', '456 Elm St, Shelbyville', 'owner2.jpg', now(), '1990-03-22'),
('Carlos Lopez', '789 Oak St, Capital City', NULL, now(), '1980-11-05');
```
## Insertar Datos en la Tabla Property

```sql
INSERT INTO public."Property" ("Name", "Address", "Price", "Created_at", "CodeInternal", "Year", "IdOwner") VALUES
('Beautiful Bungalow', '321 Pine St, Springfield', 250000, now(), 'BB2025', 2020, 1),
('Modern Apartment', '654 Maple St, Shelbyville', 350000, now(), 'MA2026', 2021, 2),
('Cozy Cottage', '987 Willow St, Capital City', 150000, now(), 'CC2027', 2019, 3);
```
## Insertar Datos en la Tabla PropertyTrace

```sql
INSERT INTO public."PropertyTrace" ("DateSale", "Name", "Value", "Tax", "IdProperty", "Created_at") VALUES
('2024-01-10', 'Initial Sale', 250000, 0.05, 1, now()),
('2024-01-12', 'Second Sale', 350000, 0.07, 2, now()),
('2024-01-15', 'First Sale', 150000, 0.04, 3, now());
```


##  Requerimientos a Evaluar

La base de datos fue diseñada para gestionar y visualizar información de propiedades inmobiliarias de manera eficiente. El sistema proporciona una interfaz web moderna , permitiendo a los usuarios consultar y administrar datos de propiedades almacenadas en la base de datos. Esta solución se implemento para una empresa del sector inmobiliario que requiere centralizar la consulta de datos relacionados con propiedades, propietarios y otros atributos asociados, como la ubicación, el precio y las imágenes de las propiedades.

### Cumplimiento de los principios ACID:

#### Atomicidad: 
Las operaciones son atómicas, garantizando que las transacciones se realicen de forma completa o no se realicen en absoluto. Si un paso de la operación falla, toda la transacción se revierte, asegurando la coherencia de los datos.

#### Consistencia: 

La base de datos siempre permanecerá en un estado consistente, con restricciones de claves foráneas y validaciones de datos que aseguran la integridad de la información en todo momento.

#### Durabilidad: 
Una vez que una transacción se confirma, sus efectos son permanentes, incluso en caso de fallos del sistema.
Aislamiento: Cada transacción se realiza de forma aislada, evitando interferencias de otras operaciones concurrentes, garantizando que las lecturas y escrituras sean coherentes.

#### Escalabilidad y Mantenimiento
El diseño propuesto permite la expansión del sistema sin afectar las funcionalidades existentes. Agregar nuevos países, estados o ciudades no impactará en las relaciones ya definidas entre las tablas. Además, las consultas complejas, como la búsqueda de propiedades con filtros dinámicos, serán eficientes gracias a la estructura de las tablas y el uso adecuado de índices.
