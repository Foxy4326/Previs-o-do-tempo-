<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Detecção Climática</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            color: #fff;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            margin-bottom: 40px;
            padding: 20px;
            background-color: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }

        .date-display {
            font-size: 1.2rem;
            opacity: 0.9;
        }

        .weather-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 40px;
        }

        .weather-card {
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            padding: 25px;
            text-align: center;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease, background-color 0.3s ease;
        }

        .weather-card:hover {
            transform: translateY(-5px);
            background-color: rgba(255, 255, 255, 0.15);
        }

        .weather-icon {
            font-size: 4rem;
            margin-bottom: 15px;
        }

        .weather-title {
            font-size: 1.5rem;
            margin-bottom: 10px;
        }

        .weather-value {
            font-size: 2rem;
            font-weight: bold;
            margin-bottom: 10px;
        }

        .weather-status {
            font-size: 1.1rem;
            opacity: 0.9;
        }

        .storm-warning {
            background-color: rgba(220, 53, 69, 0.3);
            border: 2px solid #dc3545;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(220, 53, 69, 0.7); }
            70% { box-shadow: 0 0 0 10px rgba(220, 53, 69, 0); }
            100% { box-shadow: 0 0 0 0 rgba(220, 53, 69, 0); }
        }

        footer {
            text-align: center;
            padding: 20px;
            margin-top: 40px;
            background-color: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        .update-info {
            font-size: 0.9rem;
            opacity: 0.7;
            margin-top: 10px;
        }

        /* Cores específicas para cada condição */
        .temperature-card {
            border-left: 5px solid #ff9a3c;
        }

        .rain-card {
            border-left: 5px solid #3c8dbc;
        }

        .wind-card {
            border-left: 5px solid #78c2ad;
        }

        .storm-card {
            border-left: 5px solid #dc3545;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Sistema de Detecção Climática</h1>
            <div class="date-display" id="currentDate">Carregando data...</div>
        </header>

        <main>
            <div class="weather-grid">
                <div class="weather-card temperature-card">
                    <div class="weather-icon">🌡️</div>
                    <h2 class="weather-title">Temperatura</h2>
                    <div class="weather-value" id="temperatureValue">--°C</div>
                    <div class="weather-status" id="temperatureStatus">--</div>
                </div>

                <div class="weather-card rain-card">
                    <div class="weather-icon">🌧️</div>
                    <h2 class="weather-title">Chuva</h2>
                    <div class="weather-value" id="rainValue">-- mm</div>
                    <div class="weather-status" id="rainStatus">--</div>
                </div>

                <div class="weather-card wind-card">
                    <div class="weather-icon">💨</div>
                    <h2 class="weather-title">Vento</h2>
                    <div class="weather-value" id="windValue">-- km/h</div>
                    <div class="weather-status" id="windStatus">--</div>
                </div>

                <div class="weather-card storm-card" id="stormCard">
                    <div class="weather-icon">⛈️</div>
                    <h2 class="weather-title">Tempestade</h2>
                    <div class="weather-value" id="stormValue">--</div>
                    <div class="weather-status" id="stormStatus">--</div>
                </div>
            </div>
        </main>

        <footer>
            <p>Sistema de monitoramento climático automático - Atualização diária</p>
            <div class="update-info" id="updateInfo">Próxima atualização: --</div>
        </footer>
    </div>

    <script>
        // Função para formatar a data
        function formatDate(date) {
            const options = { 
                weekday: 'long', 
                year: 'numeric', 
                month: 'long', 
                day: 'numeric' 
            };
            return date.toLocaleDateString('pt-BR', options);
        }

        // Função para gerar dados climáticos baseados no dia do ano
        function generateWeatherData() {
            const today = new Date();
            const dayOfYear = Math.floor((today - new Date(today.getFullYear(), 0, 0)) / (1000 * 60 * 60 * 24));
            
            // Usar o dia do ano como seed para gerar dados consistentes por dia
            const seed = dayOfYear;
            
            // Gerar temperatura baseada na estação do ano
            let baseTemp;
            const month = today.getMonth();
            if (month >= 2 && month <= 4) { // Primavera
                baseTemp = 18 + (seed % 15);
            } else if (month >= 5 && month <= 7) { // Verão
                baseTemp = 25 + (seed % 15);
            } else if (month >= 8 && month <= 10) { // Outono
                baseTemp = 15 + (seed % 12);
            } else { // Inverno
                baseTemp = 8 + (seed % 10);
            }
            
            // Gerar chuva baseada no mês
            let rainProb;
            if (month >= 10 || month <= 3) { // Período mais chuvoso
                rainProb = 0.6;
            } else {
                rainProb = 0.3;
            }
            
            const hasRain = Math.sin(seed * 0.5) > rainProb ? 0 : (Math.abs(Math.sin(seed * 0.3)) * 50);
            
            // Gerar vento
            const windSpeed = 5 + (Math.abs(Math.cos(seed * 0.7)) * 35);
            
            // Determinar status da tempestade
            let stormStatus = "Sem tempestade";
            let stormAlert = false;
            
            if (hasRain > 30 && windSpeed > 25) {
                stormStatus = "ALERTA DE TEMPESTADE!";
                stormAlert = true;
            } else if (hasRain > 20 && windSpeed > 20) {
                stormStatus = "Possibilidade de tempestade";
            }
            
            return {
                temperature: Math.round(baseTemp),
                rain: Math.round(hasRain * 10) / 10,
                wind: Math.round(windSpeed * 10) / 10,
                storm: stormStatus,
                stormAlert: stormAlert
            };
        }

        // Função para atualizar a interface
        function updateWeatherDisplay() {
            const weatherData = generateWeatherData();
            const now = new Date();
            
            // Atualizar data
            document.getElementById('currentDate').textContent = formatDate(now);
            
            // Atualizar temperatura
            document.getElementById('temperatureValue').textContent = `${weatherData.temperature}°C`;
            let tempStatus = "Normal";
            if (weatherData.temperature > 30) tempStatus = "Quente";
            if (weatherData.temperature > 35) tempStatus = "Muito Quente";
            if (weatherData.temperature < 10) tempStatus = "Frio";
            if (weatherData.temperature < 5) tempStatus = "Muito Frio";
            document.getElementById('temperatureStatus').textContent = tempStatus;
            
            // Atualizar chuva
            document.getElementById('rainValue').textContent = `${weatherData.rain} mm`;
            let rainStatus = "Sem chuva";
            if (weatherData.rain > 0.1 && weatherData.rain <= 5) rainStatus = "Chuva leve";
            if (weatherData.rain > 5 && weatherData.rain <= 15) rainStatus = "Chuva moderada";
            if (weatherData.rain > 15) rainStatus = "Chuva forte";
            document.getElementById('rainStatus').textContent = rainStatus;
            
            // Atualizar vento
            document.getElementById('windValue').textContent = `${weatherData.wind} km/h`;
            let windStatus = "Calmo";
            if (weatherData.wind > 10 && weatherData.wind <= 20) windStatus = "Vento moderado";
            if (weatherData.wind > 20 && weatherData.wind <= 30) windStatus = "Vento forte";
            if (weatherData.wind > 30) windStatus = "Vento muito forte";
            document.getElementById('windStatus').textContent = windStatus;
            
            // Atualizar tempestade
            document.getElementById('stormValue').textContent = weatherData.stormAlert ? "ATIVO" : "INATIVO";
            document.getElementById('stormStatus').textContent = weatherData.storm;
            
            // Aplicar efeito de alerta se necessário
            const stormCard = document.getElementById('stormCard');
            if (weatherData.stormAlert) {
                stormCard.classList.add('storm-warning');
            } else {
                stormCard.classList.remove('storm-warning');
            }
            
            // Calcular próxima atualização (meia-noite)
            const tomorrow = new Date(now);
            tomorrow.setDate(tomorrow.getDate() + 1);
            tomorrow.setHours(0, 0, 0, 0);
            
            const timeUntilUpdate = tomorrow - now;
            const hours = Math.floor(timeUntilUpdate / (1000 * 60 * 60));
            const minutes = Math.floor((timeUntilUpdate % (1000 * 60 * 60)) / (1000 * 60));
            
            document.getElementById('updateInfo').textContent = 
                `Próxima atualização: ${hours}h ${minutes}m`;
        }

        // Inicializar e atualizar a cada minuto para mostrar a contagem regressiva
        updateWeatherDisplay();
        setInterval(updateWeatherDisplay, 60000);
    </script>
</body>
</html>