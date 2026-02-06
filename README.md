# Importación de Centros Educativos: CSV a PostgreSQL

Este proyecto automatiza la carga de datos desde un archivo `listado.csv` hacia una base de datos PostgreSQL. El script está diseñado para manejar la codificación de caracteres española y asegurar la integridad de los datos mediante transacciones.

## 📌 Resumen del Proyecto

El sistema toma un archivo con información de centros docentes y los organiza en una tabla estructurada.



### Mapeo de Columnas (CSV -> Base de Datos)
Para que los datos se guarden correctamente, el script utiliza los siguientes índices del CSV:
* **ID (Serial):** Generado automáticamente por PostgreSQL.
* **Nombre:** Proviene de la columna `Código` (índice 0).
* **Domicilio:** Proviene de la columna `Denominación` (índice 1).
* **Localidad:** Proviene de la columna `Nombre` (índice 2).
* **CP:** Proviene de la columna `Dependencia` (índice 3).
* **Teléfono:** Proviene de la columna `Domicilio` (índice 4).

## 🛠️ Requisitos e Instalación

1.  **Instalar dependencias:**
    ```bash
    pip install pandas psycopg2
    ```
2.  **Preparar el archivo:** Asegúrate de que el archivo se llame `listado.csv` y esté en la misma carpeta que el script.
3.  **Configurar credenciales:** El script usa por defecto `host: localhost`, `user: odoo` y `pass: odoo`.

## 💻 El Código (Script de Python)

[código](visual.png)

## Resultado

[resultado](docker.png)
