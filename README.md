<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Thermodynamics Simulation</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha3/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Google Font -->
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary-color: #007bff;
            --background-color: rgba(0, 0, 0, 0.8);
            --text-color: #fff;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background: url('https://www.toptal.com/designers/subtlepatterns/patterns/math-dark.png') no-repeat center center fixed;
            background-size: cover;
            color: var(--text-color);
        }

        header {
            background: rgba(var(--primary-color), 0.9);
            padding: 20px;
            text-align: center;
            border-bottom: 2px solid var(--text-color);
        }

        .container {
            margin-top: 30px;
            background: var(--background-color);
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
        }

        .form-control, .form-select {
            background: rgba(255, 255, 255, 0.8);
        }

        button {
            transition: all 0.3s ease;
        }

        button:hover {
            transform: scale(1.05);
            background-color: #0056b3 !important;
        }

        footer {
            background: rgba(var(--primary-color), 0.9);
            padding: 10px;
            text-align: center;
            margin-top: 30px;
            border-top: 2px solid var(--text-color);
        }

        .language-switch {
            text-align: right;
        }

        .graph-container {
            margin-top: 20px;
        }

        @media (max-width: 768px) {
            .container {
                padding: 10px;
            }

            h1 {
                font-size: 1.5rem;
            }

            h2 {
                font-size: 1.2rem;
            }
        }
    </style>
</head>
<body>
    <header>
        <h1 id="header-title">Thermodynamics Simulation</h1>
    </header>

    <div class="container">
        <div class="language-switch">
            <button class="btn btn-secondary btn-sm" onclick="switchLanguage('en')">English</button>
            <button class="btn btn-secondary btn-sm" onclick="switchLanguage('ru')">Русский</button>
        </div>
        <h2 id="input-title">Enter Parameters</h2>
        <div class="mb-3">
            <label for="volume" class="form-label" id="volume-label">Initial Volume (m³):</label>
            <input type="number" id="volume" class="form-control" placeholder="e.g., 1" value="1" step="0.1" min="0.1">
        </div>
        <div class="mb-3">
            <label for="pressure" class="form-label" id="pressure-label">Initial Pressure (Pa):</label>
            <input type="number" id="pressure" class="form-control" placeholder="e.g., 100000" value="100000" min="0">
        </div>
        <div class="mb-3">
            <label for="temperature" class="form-label" id="temperature-label">Temperature (K):</label>
            <input type="number" id="temperature" class="form-control" placeholder="e.g., 300" value="300" min="0">
        </div>
        <div class="mb-3">
            <label for="process" class="form-label" id="process-label">Process Type:</label>
            <select id="process" class="form-select">
                <option value="isothermal">Isothermal</option>
                <option value="adiabatic">Adiabatic</option>
                <option value="isobaric">Isobaric</option>
                <option value="isochoric">Isochoric</option>
            </select>
        </div>
        <button class="btn btn-primary w-100" onclick="generateGraph()" id="plot-button">Plot Graph</button>

        <div class="graph-container mt-4">
            <canvas id="graphCanvas"></canvas>
        </div>
    </div>

    <footer>
        <p id="footer-text">Designed for the thermodynamics project</p>
    </footer>

    <script>
        const translations = {
            en: {
                headerTitle: "Thermodynamics Simulation",
                inputTitle: "Enter Parameters",
                volumeLabel: "Initial Volume (m³):",
                pressureLabel: "Initial Pressure (Pa):",
                temperatureLabel: "Temperature (K):",
                processLabel: "Process Type:",
                plotButton: "Plot Graph",
                footerText: "Designed for the thermodynamics project"
            },
            ru: {
                headerTitle: "Моделирование термодинамических процессов",
                inputTitle: "Введите параметры",
                volumeLabel: "Начальный объем (м³):",
                pressureLabel: "Начальное давление (Па):",
                temperatureLabel: "Температура (К):",
                processLabel: "Тип процесса:",
                plotButton: "Построить график",
                footerText: "Разработано для проекта по термодинамике"
            }
        };

        let currentLanguage = 'en';

        function switchLanguage(lang) {
            currentLanguage = lang;
            const elements = [
                { id: "header-title", key: "headerTitle" },
                { id: "input-title", key: "inputTitle" },
                { id: "volume-label", key: "volumeLabel" },
                { id: "pressure-label", key: "pressureLabel" },
                { id: "temperature-label", key: "temperatureLabel" },
                { id: "process-label", key: "processLabel" },
                { id: "plot-button", key: "plotButton" },
                { id: "footer-text", key: "footerText" }
            ];
            elements.forEach(element => {
                document.getElementById(element.id).innerText = translations[lang][element.key];
            });
        }

        function generateGraph() {
            const volume = parseFloat(document.getElementById("volume").value);
            const pressure = parseFloat(document.getElementById("pressure").value);
            const temperature = parseFloat(document.getElementById("temperature").value);
            const process = document.getElementById("process").value;

            const R = 8.314; // Универсальная газовая постоянная
            const n = 1; // Количество молей
            const gamma = 1.4; // Показатель адиабаты (Cp/Cv)

            const volumes = [];
            const pressures = [];

            for (let v = 0.5; v <= 3; v += 0.1) {
                volumes.push(v.toFixed(2));
                let p;
                switch (process) {
                    case 'isothermal':
                        p = (n * R * temperature / v).toFixed(2); // Изотермический процесс
                        break;
                    case 'adiabatic':
                        p = (pressure * Math.pow(volume, gamma) / Math.pow(v, gamma)).toFixed(2); // Адиабатический процесс
                        break;
                    case 'isobaric':
                        p = pressure.toFixed(2); // Изобарный процесс
                        break;
                    case 'isochoric':
                        p = (pressure * v / volume).toFixed(2); // Изохорный процесс
                        break;
                }
                pressures.push(p);
            }

            const ctx = document.getElementById("graphCanvas").getContext("2d");
            if (window.myChart) {
                window.myChart.destroy(); // Уничтожаем предыдущий график
            }
            window.myChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: volumes,
                    datasets: [{
                        label: `${process.charAt(0).toUpperCase() + process.slice(1)} Process`,
                        data: pressures,
                        borderColor: 'blue',
                        borderWidth: 2,
                        fill: false
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: { display: true }
                    },
                    scales: {
                        x: { title: { display: true, text: 'Volume (m³)' } },
                        y: { title: { display: true, text: 'Pressure (Pa)' } }
                    }
                }
            });
        }

        // Инициализация языка по умолчанию
        switchLanguage('en');
    </script>
</body>
</html>
