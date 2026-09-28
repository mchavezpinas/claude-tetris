# Claude Code Project Skills

Este directorio contiene las skills personalizadas para el proyecto Tetris.

## Skills Disponibles

### 🌤️ `/weather`

Obtén información del clima para cualquier ubicación.

**Uso:**
```bash
/weather
```

**Funcionalidades:**
- Temperatura actual en °C y °F
- Condiciones climáticas (soleado, nublado, lluvia, etc.)
- Humedad relativa
- Velocidad del viento
- Información en tiempo real desde la web

**Ejemplo:**
```
Marco: /weather
Claude: ¿Para qué ubicación deseas conocer el clima?
Marco: Lima
Claude: 📍 Ubicación: Lima, Perú
         🌡️ Temperatura: 28°C / 82°F
         ☁️ Condiciones: Parcialmente nublado
         💧 Humedad: 65%
         💨 Viento: 12 km/h
```

## Cómo agregar nuevas skills

1. Crea un archivo `nombre-skill.md` en el directorio `skills/`
2. Incluye las instrucciones en formato markdown
3. Actualiza `settings.json` si es necesario
4. Usa `/nombre-skill` para invocar la skill

## Estructura de una skill

Una skill debe incluir:
- Descripción clara del propósito
- Instrucciones paso a paso
- Ejemplos de uso
- Características principales
- Notas importantes
