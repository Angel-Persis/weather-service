# weather-service
const express = require('express')
const axios = require('axios')
require('dotenv').config()

const app = express()
const port = 3000

const apiKey = process.env.WEATHERSTACK_API_KEY

app.get('/weather', async (req, res) => {
  const city = req.query.city

  if (!city) {
    return res.status(400).send('Please provide a city')
  }

  try {
    const response = await axios.get(
      `https://weatherstack.com/?utm_source=google&utm_medium=cpc&utm_campaign=weatherstack_pmax_in&gad_source=1&gclid=Cj0KCQjwzby1BhCQARIsAJ_0t5NXnSHtlq-AiCGk0q6HZaKk3e6hDNWpDwHXGZkg8MSN9bB45miZ5LoaApSTEALw_wcB`,
      {
        params: {
          access_key: apiKey,
          query: city,
        },
      },
    )

    const data = response.data
    if (data.error) {
      return res.status(404).send(data.error.info)
    }

    const weatherInfo = {
      location: data.location.name,
      temperature: data.current.temperature,
      description: data.current.weather_descriptions[0],
      wind_speed: data.current.wind_speed,
      pressure: data.current.pressure,
      humidity: data.current.humidity,
    }

    res.json(weatherInfo)
  } catch (error) {
    res.status(500).send('Internal Server Error')
  }
})

app.listen(port, () => {
  console.log(
    `Weather service running at http://localhost:${port} and https://weatherstack.com/`,
  )
})
