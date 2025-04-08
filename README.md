# 🏡 Plataforma de Reservas de Propiedades

Este proyecto es una plataforma web tipo Airbnb desarrollada con HTML, CSS y Python (Fastapi), que permite a los usuarios explorar propiedades en alquiler, ver detalles, realizar reservas y dejar retroalimentación.

## 📁 Estructura del Proyecto

```
├── page.HTML             # Página principal con listado de propiedades
├── detalle.html          # Detalles individuales de cada propiedad
├── reserva.html          # Formulario para completar la reserva
├── reserva-bef.html      # Página previa a la reserva
├── Mis-reservas.html     # Historial de reservas del usuario
├── feedback.html         # Sección para comentarios y retroalimentación
├── styles.css            # Estilos personalizados
├── main.py               # Backend con Flask (gestión de rutas y lógica)
```

## 🚀 Características Principales

- 🎯 **Explorar Propiedades**: Visualiza propiedades con imagen, precio y ubicación.
- 📍 **Detalles Ampliados**: Muestra imágenes grandes, mapas y características de la propiedad.
- 📆 **Reservas en Línea**: Completa una reserva con formulario y simula pago.
- 💬 **Retroalimentación**: Sección para comentarios de los usuarios.
- 🔐 **Gestión de Reservas**: Historial de reservas personales (Mis-reservas).
- 🎨 **Diseño Responsivo**: Visual atractivo con estilos modernos y adaptados.

## 🛠️ Tecnologías Usadas

- **Frontend**: HTML5, CSS3
- **Backend**: Python 3 (Fastapi)
- **Otros**: Google Maps (para ubicaciones)

## ▶️ Cómo Ejecutarlo

1. Instala Flask:
   ```bash
   pip install fastapi
   ```

2. Ejecuta el servidor:
   ```bash
   python -m uvicorn main:app --reload
   ```

3. Abre el navegador y ve a:
   ```
   http://localhost:5000
   ```

## 📌 Pendientes o Mejoras Futuras

- Integración con servicios de pago.

## 📷 Vista previa

Se puede incluir una o varias capturas de pantalla aquí (opcional si vas a subirlo a GitHub).
