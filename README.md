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

A continuación se muestra el código completo utilizado para la migración:

```python
import pandas as pd
import psycopg2
import os

# 1. Configuración de conexión
params = {
    "host": "localhost",
    "port": 5432,
    "database": "postgres",
    "user": "odoo",
    "password": "odoo"
}

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
ruta_csv = os.path.join(BASE_DIR, "listado.csv")

try:
    # Lectura con codificación específica para caracteres españoles
    df = pd.read_csv(ruta_csv, encoding="latin1")
    print("✅ Archivo listado.csv leído correctamente.")

    conn = psycopg2.connect(**params)
    cur = conn.cursor()
    print("✅ Conexión con PostgreSQL establecida.")

    # 2. Creación de la tabla
    cur.execute("""
        CREATE TABLE IF NOT EXISTS contactos_mailing (
            id SERIAL PRIMARY KEY,
            nombre TEXT,
            domicilio TEXT,
            localidad TEXT,
            cp TEXT,
            telefono TEXT
        );
    """)

    # 3. Inserción de datos (Mapeo por índice de columna)
    for _, row in df.iterrows():
        cur.execute("""
            INSERT INTO contactos_mailing (nombre, domicilio, localidad, cp, telefono)
            VALUES (%s, %s, %s, %s, %s)
        """, (
            str(row.iloc[0]), # Código -> nombre
            str(row.iloc[1]), # Denominación -> domicilio
            str(row.iloc[2]), # Nombre -> localidad
            str(row.iloc[3]), # Dependencia -> cp
            str(row.iloc[4])  # Domicilio -> telefono
        ))

    conn.commit()
    print(f"🚀 ¡Éxito! Se han importado {len(df)} registros.")

except Exception as e:
    print(f"❌ Error: {e}")
    if 'conn' in locals(): conn.rollback()
finally:
    if 'cur' in locals(): cur.close()
    if 'conn' in locals(): conn.close()
