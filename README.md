# Semana 14 · Docker y Contenedores


API Flask que analiza texto: cuenta palabras, saca las más frecuentes y genera un resumen corto. Corre dentro de un contenedor Docker y se levanta con Docker Compose.


## Estructura


| Archivo | Para qué sirve |
|---|---|
| procesador.py | Funciones de análisis: contar_palabras, frecuencia_palabras, resumen |
| app.py | API Flask con los endpoints /analizar (POST) y /health |
| test_procesador.py | Tests unitarios del procesador |
| requirements.txt | Dependencias (Flask 3.0.0) |
| Dockerfile | Construye la imagen sobre python:3.11-slim y corre los tests en el build |
| .dockerignore | Deja fuera del build cachés, .git, .env y entornos virtuales |
| docker-compose.yml | Define el servicio web con puerto, volumen y variables de entorno |


## Cómo funciona


app.py recibe el texto por HTTP y llama a las funciones de procesador.py. El Dockerfile instala primero las dependencias para aprovechar la caché de capas, después copia el código y corre los tests. Si algún test falla, la imagen no se construye.


## Requisitos


Docker Desktop con el motor en ejecución
## Ejecutar con Docker Compose
bash
docker compose up -d --build
docker compose ps


Para detenerlo:
bash
docker compose down


## Ejecutar solo con Docker
bash
docker build -t procesador-texto .
docker run -d -p 5000:5000 --name mi-api procesador-texto


## Probar la API


PowerShell:
powershell
Invoke-RestMethod -Uri http://localhost:5000/analizar -Method Post -ContentType "application/json" -Body '{"texto": "Docker es una herramienta increible para el despliegue de aplicaciones"}'


Linux o Git Bash:
bash
curl -X POST http://localhost:5000/analizar -H "Content-Type: application/json" -d '{"texto": "Docker es una herramienta increible para el despliegue de aplicaciones"}'


Respuesta:
json
{
  "palabras": 10,
  "frecuencia": {"docker": 1, "es": 1, "una": 1, "herramienta": 1, "increible": 1},
  "resumen": "Docker es una herramienta increible para el despliegue de aplicaciones"
}


Si el texto llega vacío, la API responde 400 con {"error": "Texto vacio"}. El endpoint GET /health devuelve {"status": "ok"}.


## Nota sobre la red de la universidad


El pip install del Dockerfile usa --trusted-host porque la red del laboratorio intercepta HTTPS y pip rechaza el certificado. Fuera de esa red no hace falta, pero tampoco molesta.