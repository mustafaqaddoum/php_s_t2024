# Senior Full Stack Developer Test Plan

## Points and Sections Breakdown

| **Section**                                      | **Task Description**                                                         | **Points** |
|--------------------------------------------------|-----------------------------------------------------------------------------|------------|
| **1. Practical Coding (Laravel Backend)**        | Create Laravel app with OpenWeatherMap API integration                       | 40         |
|                                                  | - API Integration (20 points)                                               |            |
|                                                  | - Controller and Route (10 points)                                          |            |
|                                                  | - Store Data in Database (10 points)                                        |            |
| **2. Practical Coding (React.js Frontend)**      | Create React app to fetch and display weather data                          | 40         |
|                                                  | - Input Form (10 points)                                                    |            |
|                                                  | - Fetch Data from API (20 points)                                           |            |
|                                                  | - Display Weather Data (10 points)                                          |            |
| **3. API Connectivity and Flow**                 | Answer theoretical questions                                                | 20         |
|                                                  | - State management in React (10 points)                                     |            |
|                                                  | - Securing API endpoint in Laravel (10 points)                              |            |
| **4. Testing**                                   | Write unit and integration tests                                            | 20         |
|                                                  | - PHPUnit Test for `getWeather` method (10 points)                          |            |
|                                                  | - Jest Test for `Weather` component (10 points)                             |            |
| **5. Database Migration and Activity Log**       | Implement activity logging feature                                          | 20         |
|                                                  | - Create Migration (10 points)                                              |            |
|                                                  | - Log Activities in Controller (10 points)                                  |            |
| **6. Admin Dashboard, Middleware, Admin Users**  | Implement admin dashboard with middleware, user management, charts, webhook | 40         |
|                                                  | - Create Middleware (10 points)                                             |            |
|                                                  | - Create Admin User Management (10 points)                                  |            |
|                                                  | - Implement Admin Dashboard and Charts (15 points)                          |            |
|                                                  | - Implement Webhook for Live Data (5 points)                                |            |
| **Total**                                        |                                                                             | **180**    |

---

## Section 1: Practical Coding (Laravel Backend with Third-Party API Integration)
**Points: 40**

**Task:** Create a Laravel application that integrates with a third-party API, such as the OpenWeatherMap API, to fetch weather data for a specific city and save this data in a database.

### Steps:
1. Register on [OpenWeatherMap](https://openweathermap.org/api) to get an API key.
2. Create a route and controller to fetch weather data using the API.
3. Store the fetched data in the database along with the city name.

### Points Breakdown:
- **Creating the API Integration (20 points)**
- **Creating the Controller and Route (10 points)**
- **Storing Data in the Database (10 points)**

### Example:

1. **Install GuzzleHttp (if not installed):**
   ```bash
   composer require guzzlehttp/guzzle
   ```

2. **Create Migration for Weather Data:**
   ```bash
   php artisan make:migration create_weather_data_table
   ```

   **Migration:**
   ```php
   public function up()
   {
       Schema::create('weather_data', function (Blueprint $table) {
           $table->id();
           $table->string('city');
           $table->json('data');
           $table->timestamps();
       });
   }
   ```

3. **Model:**
   ```php
   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;

   class WeatherData extends Model
   {
       use HasFactory;

       protected $fillable = ['city', 'data'];

       protected $casts = [
           'data' => 'array',
       ];
   }
   ```

4. **Controller:**
   ```php
   namespace App\Http\Controllers;

   use Illuminate\Http\Request;
   use GuzzleHttp\Client;
   use App\Models\WeatherData;

   class WeatherController extends Controller
   {
       public function getWeather($city)
       {
           $client = new Client();
           $response = $client->get('http://api.openweathermap.org/data/2.5/weather', [
               'query' => [
                   'q' => $city,
                   'appid' => 'your_api_key_here'
               ]
           ]);
           $weatherData = json_decode($response->getBody(), true);

           // Store the weather data in the database
           WeatherData::create([
               'city' => $city,
               'data' => $weatherData,
           ]);

           return response()->json($weatherData);
       }
   }
   ```

5. **Route:**
   ```php
   Route::get('/weather/{city}', [WeatherController::class, 'getWeather']);
   ```

## Section 2: Practical Coding (React.js Frontend)
**Points: 40**

**Task:** Create a React.js application that fetches and displays weather data for a city entered by the user.

### Points Breakdown:
- **Creating the Input Form (10 points)**
- **Fetching Data from the API (20 points)**
- **Displaying Weather Data (10 points)**

### Example:
```jsx
import React, { useState } from 'react';
import axios from 'axios';

function Weather() {
  const [city, setCity] = useState('');
  const [weather, setWeather] = useState(null);
  const [error, setError] = useState('');

  const fetchWeather = () => {
    axios.get(`http://your-backend-url/weather/${city}`)
      .then(response => {
        setWeather(response.data);
        setError('');
      })
      .catch(error => {
        setError('Error fetching weather data');
      });
  };

  return (
    <div>
      <h1>Weather App</h1>
      <input
        type="text"
        value={city}
        onChange={(e) => setCity(e.target.value)}
        placeholder="Enter city"
      />
      <button onClick={fetchWeather}>Get Weather</button>
      {error && <p>{error}</p>}
      {weather && (
        <div>
          <h2>{weather.name}</h2>
          <p>{weather.weather[0].description}</p>
          <p>Temperature: {weather.main.temp}K</p>
        </div>
      )}
    </div>
  );
}

export default Weather;
```

## Section 3: API Connectivity and Flow
**Points: 20**

### Sample Questions:
1. **Explain how you handle state management in a React application that consumes a RESTful API (10 points).**
2. **Describe how you would secure an API endpoint in Laravel (10 points).**

## Section 4: Testing
**Points: 20**

**Task:** Write tests for the following scenarios:
1. Unit test for the `getWeather` method in `WeatherController` using PHPUnit.
2. Integration test for the `Weather` component using Jest and React Testing Library.

### Points Breakdown:
- **PHPUnit Test (10 points)**
- **Jest Test (10 points)**

### Example (PHPUnit):
```php
public function testGetWeather()
{
    $response = $this->get('/weather/London');
    $response->assertStatus(200);
    $response->assertJsonStructure([
        'weather' => [
            '*' => ['description']
        ],
        'main' => ['temp']
    ]);
}
```

### Example (Jest):
```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import axios from 'axios';
import MockAdapter from 'axios-mock-adapter';
import Weather from './Weather';

test('fetches and displays weather data', async () => {
  const mock = new MockAdapter(axios);
  const weatherData = {
    name: 'London',
    weather: [{ description: 'clear sky' }],
    main: { temp: 289.92 }
  };
  mock.onGet('http://your-backend-url/weather/London').reply(200, weatherData);

  render(<Weather />);
  fireEvent.change(screen.getByPlaceholderText('Enter city'), { target: { value: 'London' } });
  fireEvent.click(screen.getByText('Get Weather'));

  expect(await screen.findByText('London')).toBeInTheDocument();
  expect(screen.getByText('clear sky')).toBeInTheDocument();
  expect(screen.getByText('Temperature: 289.92K')).toBeInTheDocument();
});
```

## Section 5: Database Migration and Activity Log
**Points: 20**

**Task:** Implement a feature in the Laravel application to log user activities such as fetching weather data.

### Points Breakdown:
- **Creating the Migration (10 points)**
- **Logging Activities in the Controller (10 points)**

### Example Migration:
```php
public function up()
{
    Schema::create('activity_logs', function (Blueprint $table) {
        $table->id();
        $table->unsignedBigInteger('user_id');
        $table->string('activity');
        $table->timestamps();
    });
}
```

### Example Logging:
```php
use App\Models\ActivityLog;

public function getWeather($city)
{
    $client = new Client();
    $response = $client->get('http://api.openweathermap.org/data/2.5/weather', [
        'query' => [
            'q' => $city,
            'appid' => 'your_api_key_here'
        ]
    ]);

## Section 6: Admin Dashboard, Middleware, and Admin Users
**Points: 40**

**Task:** Implement an admin dashboard in Laravel that includes middleware to restrict access to admin users, manage admin users, display charts using Chart.js for brewers and user URLs, and receive live data via webhooks.

### Points Breakdown:
- **Creating Middleware (10 points)**
- **Creating Admin User Management (10 points)**
- **Implementing Admin Dashboard and Charts (15 points)**
- **Implementing Webhook to Receive Live Data (5 points)**

### Steps:

1. **Create Middleware:**
   ```php
   php artisan make:middleware AdminMiddleware
   ```

   **AdminMiddleware:**
   ```php
   namespace App\Http\Middleware;

   use Closure;
   use Illuminate\Support\Facades\Auth;

   class AdminMiddleware
   {
       public function handle($request, Closure $next)
       {
           if (Auth::check() && Auth::user()->is_admin) {
               return $next($request);
           }
           return redirect('/');
       }
   }
   ```

2. **Register Middleware:**
   ```php
   // In app/Http/Kernel.php
   protected $routeMiddleware = [
       // ...
       'admin' => \App\Http\Middleware\AdminMiddleware::class,
   ];
   ```

3. **Create Admin User Management:**
   ```php
   php artisan make:model Admin -m
   ```

   **Admin Model Migration:**
   ```php
   public function up()
   {
       Schema::create('admins', function (Blueprint $table) {
           $table->id();
           $table->string('name');
           $table->string('email')->unique();
           $table->timestamp('email_verified_at')->nullable();
           $table->string('password');
           $table->boolean('is_admin')->default(true);
           $table->rememberToken();
           $table->timestamps();
       });
   }
   ```

4. **Create Admin Dashboard Controller:**
   ```php
   php artisan make:controller AdminDashboardController
   ```

   **AdminDashboardController:**
   ```php
   namespace App\Http\Controllers;

   use Illuminate\Http\Request;
   use App\Models\ActivityLog;
   use Illuminate\Support\Facades\DB;

   class AdminDashboardController extends Controller
   {
       public function __construct()
       {
           $this->middleware('admin');
       }

       public function index()
       {
           // Fetch data for charts
           $brewersData = DB::table('brewers')->select('name', DB::raw('count(*) as total'))->groupBy('name')->get();
           $userUrlsData = DB::table('activity_logs')->select('activity', DB::raw('count(*) as total'))->groupBy('activity')->get();

           return view('admin.dashboard', compact('brewersData', 'userUrlsData'));
       }
   }
   ```

5. **Create Admin Dashboard View (`admin/dashboard.blade.php`):**
   ```html
   <h1>Admin Dashboard</h1>

   <h2>Brewers Chart</h2>
   <canvas id="brewersChart"></canvas>

   <h2>User URLs Chart</h2>
   <canvas id="userUrlsChart"></canvas>

   <script>
       // JavaScript code to initialize and render charts using Chart.js
   </script>
   ```

6. **Implement Webhook for Live Data:**
   ```php
   // Implement webhook logic here to receive live data
   ```

This section outlines the steps to implement the admin dashboard, middleware, admin user management, and integration of charts using Chart.js, along with the implementation of a webhook to receive live data.



For help, please contact:
- **Admin:** [admin@netronsolutions.com](mailto:admin@netronsolutions.com)
- **Mustafa Qadoum:** [mustafa.qadoum@netronsolutions.com](mailto:mustafa.qadoum@netronsolutions.com)
