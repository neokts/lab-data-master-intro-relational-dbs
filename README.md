![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# Lab | Fundamentos SQL – Módulo 1: Introducción a Bases de Datos Relacionales

## Introducción

Has sido contratado como especialista de datos en una empresa que busca modernizar su forma de gestionar la información. Actualmente, trabajan con hojas de cálculo que contienen datos de empleados, departamentos y proyectos. Tu tarea es diseñar un modelo relacional sencillo en Snowflake que normalice esta información, respetando las buenas prácticas de modelado de datos y la nomenclatura corporativa estandarizada (ver normativa SEAT).

## Requisitos

* Haz un ***fork*** de este repositorio.
* Clona este repositorio.

## Entrega

- Haz Commit y Push
- Crea un Pull Request (PR)
- Copia el enlace a tu PR (con tu solución) y pégalo en el campo de entrega del portal del estudiante – solo así se considerará entregado el lab

## 🧠 Desafío 1 – Identificar estructuras de bases de datos reales

### Objetivo

Reflexionar sobre cómo se organizan los datos en el mundo real y comenzar a pensar en términos de relaciones, claves primarias y foráneas.

### Actividad

1. Observa este extracto de hoja de cálculo de ejemplo:

| ID_EMPLEADO | NOMBRE_EMPLEADO | NOM_DEPTO     | NOM_PROYECTO       |
|-------------|------------------|----------------|---------------------|
| 1001        | Alicia Gómez     | Finanzas       | Migración SAP       |
| 1002        | Juan Martínez    | IT             | Portal Cliente      |
| 1001        | Alicia Gómez     | Finanzas       | Portal Cliente      |

2. Responde:
   - ¿Qué información parece repetirse?
   - El nombre del departamento, el nombre del proyecto, el nombre del empleado
   - ¿Qué datos podrían separarse en distintas tablas?
   - He saparado los datos del empleado, los datos del Departamento y los datos del Proyecto
   - ¿Qué columnas podrían servir como identificadores únicos (claves primarias)?
   - solo veo el ID_Empleado como clave primaria
   - ¿Qué relaciones ves entre los datos?
   - el ID_EMPLEADO se relaciona con el resto de tablas indicadas anteriormente

> ✅ **Entregable**: Esquema escrito a mano o digital con las entidades detectadas y relaciones sugeridas (puedes usar [https://dbdiagram.io](https://dbdiagram.io)).

## 🧩 Desafío 2 – Diseño lógico del modelo

### Entidades mínimas

- **EMPLOYEE**
  - `ID_EMPLOYEE`, `NAM_EMPLOYEE`
- **DEPARTMENT**
  - `ID_DEPARTMENT`, `NAM_DEPARTMENT`
- **PROJECT**
  - `ID_PROJECT`, `NAM_PROJECT`
- **EMPLOYEE_PROJECT**
  - `ID_EMPLOYEE`, `ID_PROJECT`

### Relaciones

- Un empleado pertenece a un departamento.
- Un empleado puede estar en varios proyectos. Un proyecto puede tener varios empleados. (M:N → tabla intermedia)

> ✅ **Entregable**: Diagrama ERD

## 🛠️ Desafío 3 – Crear modelo en Snowflake

Utiliza el esquema adecuado en zona **Silver** y aplica las **reglas de nomenclatura corporativas**:

```sql
CREATE DATABASE IF NOT EXISTS DEV_SILVER_DB;
USE DATABASE DEV_SILVER_DB;

CREATE SCHEMA IF NOT EXISTS S_HUMAN_RESOURCE;
USE SCHEMA S_HUMAN_RESOURCE;
```

### Tablas

```sql
CREATE TABLE EMPLOYEE (
  ID_EMPLOYEE NUMBER PRIMARY KEY,
  NAM_EMPLOYEE VARCHAR(100),
  ID_DEPARTMENT NUMBER
);

CREATE TABLE DEPARTMENT (
  ID_DEPARTMENT NUMBER PRIMARY KEY,
  NAM_DEPARTMENT VARCHAR(100)
);

CREATE TABLE PROJECT (
  ID_PROJECT NUMBER PRIMARY KEY,
  NAM_PROJECT VARCHAR(100)
);

CREATE TABLE EMPLOYEE_PROJECT (
  ID_EMPLOYEE NUMBER,
  ID_PROJECT NUMBER,
  PRIMARY KEY (ID_EMPLOYEE, ID_PROJECT)
);
```

> ⚠️ Nota: Aunque Snowflake no aplica restricciones `FOREIGN KEY`, debes **documentarlas**.

> ✅ **Entregable**: Archivo `create.sql` con las sentencias SQL de creación.

## 🌱 Desafío 4 – Insertar datos de ejemplo

```sql
-- Departamentos
INSERT INTO DEPARTMENT VALUES
(10, 'Finance'),
(20, 'IT');

-- Empleados
INSERT INTO EMPLOYEE VALUES
(1001, 'Alicia Gomez', 10),
(1002, 'Juan Martínez', 20);

-- Proyectos
INSERT INTO PROJECT VALUES
(3001, 'SAP Migration'),
(3002, 'Customer Portal');

-- Asignación empleados-proyectos
INSERT INTO EMPLOYEE_PROJECT VALUES
(1001, 3001),
(1002, 3002),
(1001, 3002);
```

> ✅ **Entregable**: Archivo `seeding.sql` con los `INSERT INTO`.

## 🔎 Desafío 5 – Consultas básicas

```sql
-- Todos los empleados
SELECT * FROM EMPLOYEE;

-- Proyectos y empleados involucrados
SELECT 
  e.NAM_EMPLOYEE, 
  p.NAM_PROJECT
FROM EMPLOYEE_PROJECT ep
JOIN EMPLOYEE e ON ep.ID_EMPLOYEE = e.ID_EMPLOYEE
JOIN PROJECT p ON ep.ID_PROJECT = p.ID_PROJECT;
```

> ✅ **Entregable**: Captura de resultados.

## 📏 Buenas prácticas de nomenclatura

* Campos con prefijos: `ID_`, `NAM_`, `CAT_`, `TST_`, etc.
* Tablas en inglés, claves primarias no nulas.
* Modelos normalizados hasta 3FN.
* No relaciones m\:n directas: usar tablas intermedias.
* Formato de fecha: `YYYY-MM-DD`.
* Códigos de país en ISO 3166-1 alfa-3 (ej. "ESP", "MEX").

<!-- ## 📦 Entregables finales

1. `create.sql`: Definición de tablas.
2. `seeding.sql`: Datos de ejemplo.
3. Imagen ERD (`.png` / `.jpg`).
4. Capturas o `.csv` de consultas.

> 📁 Carpeta del proyecto: `lab-sql-mod1`

## 🏁 Entrega

1. Sube los archivos a tu repositorio personal.
2. Haz una **pull request** hacia el repositorio de clase.
3. Usa este formato para el título: `[lab-sql-mod1] Tu Nombre`. -->

## Entregables

Dentro de tu repositorio forkeado, asegúrate de añadir los siguientes archivos:

* `create.sql` – Definición del modelo relacional y las tablas
* `seeding.sql` – Sentencias `INSERT` con datos de ejemplo
* `diagram.png` o `diagram.jpg` – Imagen del diagrama ERD (puedes exportarlo desde [https://dbdiagram.io](https://dbdiagram.io))
* `lab-notes.md` – Documento que explique:
  * Qué entidades y relaciones identificaste en el desafío 1
  * Qué reglas aplicaste en el diseño lógico
  * Qué claves primarias y foráneas se definieron (aunque no se apliquen en Snowflake)
* (Opcional) Capturas de pantalla de las consultas realizadas

## 🎉 ¡Buen trabajo!

Este ha sido tu primer paso para entender cómo se estructura una base de datos. A partir de aquí, profundizaremos en claves externas, normalización avanzada y relaciones complejas.
