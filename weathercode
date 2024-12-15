using System;
using System.Device.Gpio;
using System.Device.I2c;
using Microsoft.Azure.Devices.Client;
using Newtonsoft.Json;
using System.Text;
using System.Threading;
using Iot.Device.Dhtxx;
using Iot.Device.Bmxx80;
using System.Device.I2c;

class Program
{
    private static DeviceClient deviceClient;
    private static string connectionString = "<Your IoT Hub Device Connection String>";

    static async Task Main(string[] args)
    {
        // Initialize Azure IoT Hub Client
        deviceClient = DeviceClient.CreateFromConnectionString(connectionString, TransportType.Mqtt);

        // Initialize DHT22 (Temperature and Humidity)
        var dht22 = new Dht22(4);  // GPIO Pin 4

        // Initialize BMP280 (Temperature and Pressure)
        var i2cSettings = new I2cConnectionSettings(1, 0x76); // I2C bus 1, BMP280 address
        var i2cDevice = I2cDevice.Create(i2cSettings);
        var bmp280 = new Bmp280(i2cDevice);

        // Continuously collect and send data
        while (true)
        {
            try
            {
                // Read data from sensors
                var temperature = dht22.Temperature;
                var humidity = dht22.Humidity;
                var pressure = bmp280.Pressure;
                var tempBmp280 = bmp280.Temperature;

                // Prepare the message payload
                var weatherData = new
                {
                    Temperature = temperature,
                    Humidity = humidity,
                    Pressure = pressure,
                    Temperature_BMP280 = tempBmp280,
                    Timestamp = DateTime.UtcNow
                };

                // Convert the data to JSON
                var messageString = JsonConvert.SerializeObject(weatherData);
                var message = new Message(Encoding.UTF8.GetBytes(messageString));

                // Send the data to Azure IoT Hub
                await deviceClient.SendEventAsync(message);

                // Output to console
                Console.WriteLine($"Sent data: {messageString}");

                // Wait for 10 seconds before sending the next message
                await Task.Delay(10000); 
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error: {ex.Message}");
            }
        }
    }
}
