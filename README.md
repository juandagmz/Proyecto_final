# Proyecto_final

¿Cómo funciona?
El proyecto clasifica reseñas de productos como positivas o negativas de forma automática usando un modelo de Machine Learning, y lo integra con un flujo de automatización en Make.

El pipeline tiene dos partes:
Parte 1 — Modelo de ML (Google Colab)
El modelo se entrena con un dataset de 30 reseñas en español (15 positivas, 15 negativas). El texto de cada reseña pasa por un preprocesamiento: se convierte a minúsculas, se eliminan signos de puntuación y stopwords en español. Luego se vectoriza con TF-IDF y se clasifica con Regresión Logística. El modelo entrenado se expone como una API REST usando Flask, accesible públicamente mediante un túnel ngrok. El endpoint /clasificar recibe una reseña en JSON y devuelve el sentimiento (positiva o negativa) junto con un valor de confianza.
Parte 2 — Automatización en Make
Un escenario en Make.com monitorea un Google Sheets con reseñas. Cuando detecta una fila nueva, envía el texto al endpoint del modelo. Según la respuesta, un Router divide el flujo: si es negativa, envía una alerta por Gmail y Telegram; si es positiva, la guarda en una hoja de reseñas destacadas. En ambos casos, registra todo en una hoja consolidada con el sentimiento incluido.

¿Cómo reproducirlo?
Requisitos previos:
  -Cuenta en Google Colab
  -Cuenta gratuita en ngrok.com (para obtener el token)
  -Cuenta en Make.com
  -Cuenta de Gmail conectada a Make
  -Bot de Telegram creado con @BotFather

Paso 1 — Ejecutar el notebook

Abre Proyecto_final.ipynb en Google Colab
Reemplaza el token de ngrok en la celda correspondiente con el tuyo
Ejecuta todas las celdas en orden (Runtime → Run all)
Copia la URL pública que genera ngrok (ej: https://volatile-enforced-plaza.ngrok-free.dev)
Deja la última celda corriendo — es el servidor Flask

Paso 2 — Preparar Google Sheets
Crea un archivo con tres hojas:

Hoja principal: columnas producto, cliente, reseña, fecha
Hoja destacadas: columnas producto, cliente, reseña, fecha
Hoja consolidada: columnas producto, cliente, reseña, fecha, sentimiento

Paso 3 — Configurar el escenario en Make

Crea un nuevo escenario
Agrega los módulos en este orden: Google Sheets (Watch Rows) → HTTP (POST a /clasificar) → Router → rama negativa (Gmail + Telegram) / rama positiva (Sheets destacadas) → Google Sheets consolidado
Configura los filtros del router con la condición sentimiento equal to negativa/positiva
Haz clic en Run once para probar

Capturas

![Notebook corriendo](https://i.imgur.com/wNq8Mvr.png)

![Resultado del clasificador](https://i.imgur.com/wNq8Mvr.png)

![Canvas de Make](https://i.imgur.com/vLbu56X.png)

![Correo](https://i.imgur.com/bCrGASV.png)

![Telegram](https://i.imgur.com/DOIuiqg.png)

