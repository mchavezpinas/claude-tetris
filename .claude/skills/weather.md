# weather

Get current weather information for a specific location.

## Instructions

When the user invokes `/weather`, follow these steps:

1. **Ask for location** (if not provided in args):
   - Request the city name or location from the user
   - Store it for the weather query

2. **Fetch weather data**:
   - Use WebSearch to find current weather information for the specified location
   - Search for: "[location] current weather temperature conditions"

3. **Present the information clearly**:
   - Temperature (°C/°F)
   - Weather conditions (sunny, cloudy, rainy, etc.)
   - Humidity (if available)
   - Wind speed (if available)
   - Any weather alerts or warnings

4. **Format the response**:
   ```
   📍 Location: [City/Area]
   🌡️ Temperature: [XX°C / XX°F]
   ☁️ Conditions: [Description]
   💧 Humidity: [XX%]
   💨 Wind: [XX km/h]
   ```

## Usage

```bash
/weather
# Then provide your location

# Or with location as argument (if supported):
/weather Lima
```

## Features

- Works for any location globally
- Provides current conditions
- Shows temperature, humidity, and wind information
- Simple, readable format
- Uses only web search (no external API keys needed)

## Notes

- Requires internet connection for weather data
- Local timezone is automatically considered
- Data is fetched in real-time from web sources
