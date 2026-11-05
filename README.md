# Práctica 8 — CI/CD con GitHub Actions

Proyecto ejecutable para Windows + Visual Studio Code. El modelo está entrenado con datos sintéticos para fines docentes; no usar con clientes reales.

## 1. Ejecutar localmente (CMD de VS Code)

```bat
python -m venv .venv
.venv\Scripts\activate.bat
python -m pip install -r requirements.txt
python -m pytest -v
python -m uvicorn app.main:app --reload
```

Abrir `http://127.0.0.1:8000/`, `/docs` y `/health`.

## 2. Probar Docker

```bat
docker build -t telco-api:v1 .
docker run --rm -p 8000:8000 --name telco-api telco-api:v1
```

## 3. Publicar en GitHub

Crear un repositorio vacío llamado `telco-churn-cicd` en GitHub. Sustituir `TU_USUARIO`:

```bat
git init -b main
git add .
git commit -m "Proyecto inicial CI CD"
git remote add origin https://github.com/TU_USUARIO/telco-churn-cicd.git
git push -u origin main
```

En la pestaña Actions observar los jobs `test` y `docker`. La publicación ocurre solo en push a main después de que pasen las pruebas. En un Pull Request se ejecutan pruebas, pero no se publica la imagen.

## 4. Provocar y corregir un fallo

En `tests/test_health.py`, cambia temporalmente `assert response.status_code == 200` por `assert response.status_code == 404`; haz commit y push. Comprueba que `test` falla y `docker` no se ejecuta. Restaura `200`, haz commit y push, y comprueba la ejecución exitosa.

## 5. Descargar desde GHCR

La imagen será `ghcr.io/tu_usuario/telco-churn-cicd:latest`, con el propietario en minúsculas. Si el paquete es privado, autenticar Docker antes de descargarlo.

```bat
docker pull ghcr.io/tu_usuario/telco-churn-cicd:latest
docker run --rm -p 8000:8000 ghcr.io/tu_usuario/telco-churn-cicd:latest
```

Proyecto Telco Customer Churn con integración continua.

## Evidencias
Capturas de pruebas locales, workflow exitoso, workflow fallido, workflow corregido, paquete GHCR y predicción desde contenedor.

## Notas
- `models/champion.joblib` ya está entrenado e incluye preprocesamiento y regresión logística.
- Usa cinco variables: `tenure`, `MonthlyCharges`, `TotalCharges`, `Contract`, `InternetService`.
- Los datos de entrenamiento son artificiales. No es un modelo comercial.
- El workflow implementa pruebas y publicación de imagen (Continuous Delivery), no despliegue automático a producción.
