// File: app/page.tsx or pages/index.tsx (Next.js with Tailwind CSS and TypeScript)

"use client";

import { useEffect, useState } from "react";

interface City {
  name: string;
  country: string;
  timezone: string;
  coordinates: { lon: number; lat: number };
}

interface WeatherData {
  main: {
    temp: number;
    humidity: number;
    pressure: number;
    temp_max: number;
    temp_min: number;
  };
  weather: {
    description: string;
    icon: string;
  }[];
  wind: {
    speed: number;
  };
}

export default function Home() {
  const [cities, setCities] = useState<City[]>([]);
  const [search, setSearch] = useState("");
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [selectedWeather, setSelectedWeather] = useState<WeatherData | null>(null);

  useEffect(() => {
    const fetchCities = async () => {
      setLoading(true);
      const res = await fetch(
        `https://public.opendatasoft.com/api/records/1.0/search/?dataset=geonames-all-cities-with-a-population-1000&q=${search}&rows=20&start=${page * 20}`
      );
      const data = await res.json();
      const newCities = data.records.map((rec: any) => ({
        name: rec.fields.name,
        country: rec.fields.cou_name_en,
        timezone: rec.fields.timezone,
        coordinates: {
          lon: rec.fields.coordinates[1],
          lat: rec.fields.coordinates[0],
        },
      }));
      setCities((prev) => [...prev, ...newCities]);
      setLoading(false);
    };
    fetchCities();
  }, [page, search]);

  const fetchWeather = async (city: City) => {
    const apiKey = "YOUR_OPENWEATHERMAP_API_KEY";
    const res = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?lat=${city.coordinates.lat}&lon=${city.coordinates.lon}&units=metric&appid=${apiKey}`
    );
    const data = await res.json();
    setSelectedWeather(data);
  };

  const handleScroll = (e: React.UIEvent<HTMLDivElement>) => {
    const bottom =
      e.currentTarget.scrollHeight - e.currentTarget.scrollTop ===
      e.currentTarget.clientHeight;
    if (bottom && !loading) setPage((p) => p + 1);
  };

  return (
    <main className="min-h-screen p-6 bg-gradient-to-b from-blue-100 to-blue-200">
      <h1 className="text-3xl font-bold mb-6 text-center text-blue-800">
        🌤️ Weather Forecast App
      </h1>
      <input
        className="w-full mb-6 p-3 border rounded shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-400"
        placeholder="Search for a city..."
        value={search}
        onChange={(e) => {
          setSearch(e.target.value);
          setCities([]);
          setPage(0);
        }}
      />
      <div
        className="h-96 overflow-y-scroll rounded-lg border bg-white shadow"
        onScroll={handleScroll}
      >
        <table className="min-w-full text-sm">
          <thead className="bg-blue-300 text-blue-900">
            <tr>
              <th className="p-3 text-left">City</th>
              <th className="p-3 text-left">Country</th>
              <th className="p-3 text-left">Timezone</th>
            </tr>
          </thead>
          <tbody>
            {cities.map((city, index) => (
              <tr
                key={index}
                className="border-t hover:bg-blue-100 cursor-pointer"
                onClick={() => fetchWeather(city)}
                onContextMenu={(e) => {
                  e.preventDefault();
                  window.open(
                    `/weather?lat=${city.coordinates.lat}&lon=${city.coordinates.lon}`,
                    "_blank"
                  );
                }}
              >
                <td className="p-3 text-blue-600 underline">{city.name}</td>
                <td className="p-3">{city.country}</td>
                <td className="p-3">{city.timezone}</td>
              </tr>
            ))}
            {loading && (
              <tr>
                <td className="p-3 text-center" colSpan={3}>
                  Loading...
                </td>
              </tr>
            )}
          </tbody>
        </table>
      </div>

      {selectedWeather && (
        <div className="mt-6 p-6 bg-white rounded-lg shadow text-blue-800">
          <h2 className="text-xl font-semibold mb-2">🌡️ Weather Details</h2>
          <div className="flex gap-6 flex-wrap">
            <div>Condition: {selectedWeather.weather[0].description}</div>
            <div>Temp: {selectedWeather.main.temp}°C</div>
            <div>Humidity: {selectedWeather.main.humidity}%</div>
            <div>Pressure: {selectedWeather.main.pressure} hPa</div>
            <div>Wind: {selectedWeather.wind.speed} m/s</div>
            <div>
              Min/Max: {selectedWeather.main.temp_min}°C / {selectedWeather.main.temp_max}°C
            </div>
          </div>
        </div>
      )}
    </main>
  );
}
