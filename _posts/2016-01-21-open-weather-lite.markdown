---
layout: post
title: OpenWeatherLite
summary: OpenWeatherMap ruby api wrapper. Intentionally simple :)
---

Inspired by a raspberry pi project from one that was collecting dust. Goal of
the project was to create a simple circuit that would light up the appropriate led
based on the current weather conditions. In my search to find a good, preferably free,
api provider for weather I stumbled upon [openweathermap](https://openweathermap.org).

After registering for a free api key, I wrote this ruby class to interface between
my raspberry pi and the weather api. I had it ping every 30 seconds to get the latest
weather and turn on an led or begin pulsating the led based on temperate and weather conditions.

Here are the details below.

[![Build Status](https://travis-ci.org/zsyed91/openweatherlite.svg)](https://travis-ci.org/zsyed91/openweatherlite)

## Installation

Add this line to your application's Gemfile:

{% highlight ruby %}
gem 'openweatherlite'
{% endhighlight %}

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install openweatherlite

## Example

{% highlight ruby %}
weather = OpenWeatherLite::Weather.new('api_key')
weather.by_zip_code(60606)
{% endhighlight %}

## Usage

### `OpenWeatherLite::Weather`
This class is the api class for openweather

#### attributes
- `api_key`
- `units` (metric, imperial)

#### initialize
Provide an optional api_key

{% highlight ruby %}
# With optional api_key
OpenWeatherLite::Weather.new('api_key')

# Without api key
weather = OpenWeatherLite::Weather.new
weather.api_key = 'api_key'
{% endhighlight %}

#### by_zip_code
Provide `zip_code` and optionally `country`. `country` defaults to `us`. Returns `OpenWeatherLite::WeatherResponse`

{% highlight ruby %}
weather = OpenWeatherLite::Weather.new('api_key')
# returns OpenWeatherLite::WeatherResponse
response = weather.by_zip_code(60606)
{% endhighlight %}

#### by_city_id
Provide a `city_id` according to openweathermap and returns `OpenWeatherLite::WeatherResponse`

{% highlight ruby %}
# returns OpenWeatherLite::WeatherResponse
weather.by_city_id(2172797)
{% endhighlight %}

#### by_coords
Provide `latitude` and `longitude` and returns `OpenWeatherLite::WeatherResponse`

{% highlight ruby %}
# returns OpenWeatherLite::WeatherResponse
weather.by_coords(35, 139)
{% endhighlight %}

### `OpenWeatherLite::WeatherResponse`

#### coords
Returns a hash of the coordinates

{% highlight ruby %}
response.coords
# { latitude: 123, longitude: 123 }
{% endhighlight %}

#### temperature
Returns the temperature value from the reponse. Unit is in units provided by to `OpenWeatherLite::Weather`

{% highlight ruby %}
  response.temperature
{% endhighlight %}

#### pressure
Returns the pressure value from the response

{% highlight ruby %}
  response.pressure
{% endhighlight %}

#### humidity
Returns the humidity value from the response

{% highlight ruby %}
  response.humidity
{% endhighlight %}

#### temperature_range
Returns an `array` with the `[min, max]` values from the response

{% highlight ruby %}
  response.temperature_range
{% endhighlight %}

#### wind
Returns the wind details `hash` with `speed` and `deg` from the response

{% highlight ruby %}
  response.wind
{% endhighlight %}

#### cloudy?
Returns `true` or `false` if there are any clouds

{% highlight ruby %}
  response.cloudy?
{% endhighlight %}
#### clouds
Returns the percentage of clouds in a `hash`

{% highlight ruby %}
  response.clouds
{% endhighlight %}

#### rainy?
Returns `true` or `false` depending on whether it is, or has rained in the past 3 hours

#### rain
Returns the amount that it has rained in the last 3 hours in a hash

{% highlight ruby %}
  response.rain
  # { '3h' => .445 }
{% endhighlight %}

#### snow?
Returns `true` or `false` depending on whether it is, or has rained in the past 3 hours

{% highlight ruby %}
  response.snowy?
{% endhighlight %}

#### snow
Returns the amount that it has snowed in the last 3 hours in a hash

{% highlight ruby %}
  response.snow
  # { '3h' => .445 }
{% endhighlight %}

#### sunrise
Returns the time for sunrise today

{% highlight ruby %}
  response.sunrise
{% endhighlight %}

#### sunset
Returns the time for sunset today

{% highlight ruby %}
  response.sunset
{% endhighlight %}

### Source
Github source code: [OpenWeatherLite](https://github.com/zsyed91/openweatherlite)
