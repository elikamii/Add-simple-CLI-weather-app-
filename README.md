import requests
import sys

class WeatherApp:
    """A simple CLI weather application using OpenWeatherMap API."""

    def __init__(self, api_key):
        self.api_key = api_key
        self.base_url = "https://api.openweathermap.org/data/2.5/weather"

    def get_weather(self, city):
        """Fetch weather data for the given city."""
        params = {
            "q": city,
            "appid": self.api_key,
            "units": "metric",
            "lang": "en"
        }
        try:
            response = requests.get(self.base_url, params=params, timeout=5)
            response.raise_for_status()
            data = response.json()
            return {
                "city": data["name"],
                "temperature": data["main"]["temp"],
                "description": data["weather"][0]["description"]
            }
        except requests.exceptions.RequestException as e:
            print(f"❌ Error fetching weather data: {e}")
            sys.exit(1)

    def display_weather(self, weather_info):
        """Display formatted weather information."""
        print(f"🌍 City: {weather_info['city']}")
        print(f"🌡️ Temperature: {weather_info['temperature']}°C")
        print(f"☁️ Condition: {weather_info['description'].capitalize()}")

if __name__ == "__main__":
    if len(sys.argv) < 3:
        print("Usage: python weather_cli.py <API_KEY> <CITY_NAME>")
        sys.exit(1)

    api_key = sys.argv[1]
    city_name = " ".join(sys.argv[2:])
    app = WeatherApp(api_key)
    weather = app.get_weather(city_name)
    app.display_weather(weather)
