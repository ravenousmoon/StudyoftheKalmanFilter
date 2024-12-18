<h1> <b> Лабораторна робота №7. Дослідження фільтра Калмана </b> </h1>

<b>Мета:</b> Ознайомитися з роботою фільтра Калмана та принципами його застосування. Вивчити вплив різних параметрів фільтра на результати згладжування сигналу. Навчитися аналізувати результати фільтрації, враховуючи значення дисперсії шуму до та після фільтрації.

<p><b>Завдання:</b></p>
<ol>
    <li><b>Ініціалізація коду:</b>
        <ul>
            <li>Використайте код, що наданий у розділі "Теоретичні відомості", як базовий шаблон.</li>
            <li>Переконайтеся, що всі параметри фільтра Калмана (<b>F</b>, <b>H</b>, <b>Q</b>, <b>R</b>, <b>P</b>, <b>x</b>) задані правильно та відповідають початковому варіанту.</li>
        </ul>
    </li>
    <li><b>Дослідження впливу параметрів</b>
        <p>Послідовно змінюйте значення кожного з наступних параметрів фільтра Калмана:</p>
        <ul>
            <li><b>Матриця коваріації шуму процесу (Q):</b> збільшуйте та зменшуйте значення, аналізуйте, як це впливає на передбачення.</li>
            <li><b>Матриця коваріації шуму вимірювання (R):</b> змініть цей параметр, щоб побачити, як фільтр реагує на зміну довіри до вимірювань.</li>
            <li><b>Початкова матриця коваріації (P):</b> експериментуйте з різними початковими невпевненостями щодо стану.</li>
            <li><b>Початкова оцінка стану (Initial state estimate):</b> спробуйте різні початкові значення та простежте, як це впливає на збіжність фільтра.</li>
            <li><b>Постійна складова сигналу (offset):</b> змініть зсув сигналу, щоб оцінити, наскільки фільтр адаптується до зміщеного сигналу.</li>
            <li><b>Загальний час моделювання (total_time):</b> для фільтрацій із дуже інерційними параметрами, змініть загальний час симуляції, щоб переконатися, що фільтр встигає "налаштуватися" та стабілізувати свої результати.</li>
        </ul>
    </li>
    <li><b>Порівняння результатів</b>
        <p>Для кожної комбінації параметрів:</p>
        <ul>
            <li>Розрахуйте дисперсію шуму до фільтрації та після фільтрації.</li>
            <li>Створіть графік, що показує результати фільтрації у порівнянні з реальним сигналом та шумним сигналом.</li>
            <li>Зробіть скріншоти кожного графіка та додайте їх у звіт.</li>
        </ul>
    </li>
    <li><b>Аналіз та висновки:</b>
        <ul>
            <li>Зафіксуйте значення дисперсії шуму до і після фільтрації.</li>
            <li>Для кожного випадку фільтрації прокоментуйте результати.</li>
            <li>Поясніть, як зміна конкретного параметра вплинула на передбачення, корекцію стану та зміну дисперсії.</li>
            <li>Сформулюйте загальні висновки щодо поведінки фільтра Калмана при різних комбінаціях параметрів.</li>
        </ul>
    </li>
</ol>
<p><b>Ініціалізація коду:</b> Починається з імпорту необхідних бібліотек: Flask для веб-інтерфейсу, NumPy для роботи з матрицями, а Matplotlib — для побудови графіків. Клас <b>KalmanFilter</b> визначає основні компоненти фільтра Калмана, зокрема параметри <b>F</b>, <b>H</b>, <b>Q</b>, <b>R</b>, <b>P</b> і <b>x</b>, які відповідають за динаміку процесу, вимірювання та невпевненість. Основні методи класу, <b>predict</b> і <b>update</b>, реалізують алгоритм фільтрації, де <b>predict</b> обчислює передбачення стану, а <b>update</b> коригує це передбачення на основі нових вимірювань.</p>
<p>У функції <b>index()</b> задаються значення параметрів фільтра за замовчуванням. Користувач через форму може змінити параметри та значення сигналу (амплітуду, частоту, зсув, інтервал вибірки, час). На кожному кроці фільтрації фільтр проводить обчислення <b>predict</b> і <b>update</b>, поступово скориговуючи стан системи. Згенерований графік показує реальний сигнал, зашумлений сигнал і оцінки фільтра Калмана, зберігаючись у base64 форматі для подальшого відображення на веб-сторінці. Це дозволяє візуально оцінити ефективність роботи фільтра.</p>
<p>Створюємо веб-додаток для застосування фільтра Калмана до сигналів:</p>
<p><b>HTML:</b></p>

``` html
<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kalman Filter Web App</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
</head>
<body>
    <h1>Kalman Filter</h1>
    <div class="container">
        <div class="form-container">
            <h2>Parameters</h2>
            <form method="post">
                <label>F: <input type="number" step="0.1" name="F" value="{{ F_val }}" /></label><br>
                <label>H: <input type="number" step="0.1" name="H" value="{{ H_val }}" /></label><br>
                <label>Q: <input type="number" step="0.1" name="Q" value="{{ Q_val }}" /></label><br>
                <label>R: <input type="number" step="0.1" name="R" value="{{ R_val }}" /></label><br>
                <label>P: <input type="number" step="0.1" name="P" value="{{ P_val }}" /></label><br>
                <label>x: <input type="number" step="0.1" name="x" value="{{ x_val }}" /></label><br>
                <label>Frequency: <input type="number" step="0.1" name="frequency" value="{{ frequency_val }}" /></label><br>
                <label>Amplitude: <input type="number" step="0.1" name="amplitude" value="{{ amplitude_val }}" /></label><br>
                <label>Offset: <input type="number" step="0.1" name="offset" value="{{ offset_val }}" /></label><br>
                <label>Sampling Interval: <input type="number" step="0.1" name="sampling_interval" value="{{ sampling_interval_val }}" /></label><br>
                <label>Total Time: <input type="number" step="0.1" name="total_time" value="{{ total_time_val }}" /></label><br>
                <label>Noise Variance: <input type="number" step="0.1" name="noise_variance" value="{{ noise_variance_val }}" /></label><br>
                <input type="submit" value="Apply Kalman Filter">
            </form>
        </div>
        <div class="plot-container">
            {% if plot_url %}
            <h2>Filtered Signal Plot</h2>
            <img src="data:image/png;base64,{{ plot_url }}" alt="Kalman Filter Plot">
            {% endif %}
        </div>
    </div>
</body>
</html>
```

<p><b>CSS:</b></p>

``` css
body {
    font-family: Arial, sans-serif;
    background-color: #f4f6f9;
    color: #333;
    margin: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
}

h1 {
    color: #4a90e2;
    font-size: 2rem;
    margin-bottom: 1rem;
}

form {
    background-color: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    display: flex;
    flex-direction: column;
    gap: 12px;
    max-width: 400px;
    width: 100%;
    margin-right: 20px;
}

label {
    font-weight: bold;
    margin-bottom: 4px;
}

input[type="number"] {
    width: 95%;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
    margin-top: 4px;
}

input[type="submit"] {
    background-color: #4a90e2;
    color: #fff;
    border: none;
    padding: 10px;
    border-radius: 4px;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.3s;
}

input[type="submit"]:hover {
    background-color: #357ABD;
}

h2 {
    margin-top: 20px;
    color: #333;
    text-align: left;
}

img {
    max-width: 100%;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    margin-top: 10px;
}

@media (max-width: 600px) {
    h1 {
        font-size: 1.5rem;
    }
}

.container {
    display: flex; 
    margin: 20px auto; 
    padding: 20px;
}

.form-container {
     margin-right: 50px; 
}

.plot-container {
    flex-grow: 1; 
    overflow: hidden; 
}
```

<p><b>Python:</b></p>

``` python
from flask import Flask, render_template, request
import numpy as np
import matplotlib.pyplot as plt
from io import BytesIO
import base64

class KalmanFilter:
    def __init__(self, F, H, Q, R, P, x):
        self.F = F
        self.H = H
        self.Q = Q
        self.R = R
        self.P = P
        self.x = x

    def predict(self):
        self.x = np.dot(self.F, self.x)
        self.P = np.dot(self.F, np.dot(self.P, self.F.T)) + self.Q
        return self.x

    def update(self, z):
        K = np.dot(self.P, self.H.T) / (np.dot(self.H, np.dot(self.P, self.H.T)) + self.R)
        self.x = self.x + K * (z - np.dot(self.H, self.x))
        self.P = (np.eye(len(self.P)) - K * self.H) @ self.P
        return self.x

app = Flask(__name__)

@app.route("/", methods=["GET", "POST"])
def index():
    # Значення за замовчуванням для параметрів
    F_val = 1.0
    H_val = 1.0
    Q_val = 1.0
    R_val = 10.0
    P_val = 1.0
    x_val = 0.0
    frequency_val = 1.0
    amplitude_val = 5.0
    offset_val = 10.0
    sampling_interval_val = 0.001
    total_time_val = 1.0
    noise_variance_val = 16.0

    if request.method == "POST":
        # Зчитуємо значення параметрів з форми
        F_val = float(request.form.get("F", F_val))
        H_val = float(request.form.get("H", H_val))
        Q_val = float(request.form.get("Q", Q_val))
        R_val = float(request.form.get("R", R_val))
        P_val = float(request.form.get("P", P_val))
        x_val = float(request.form.get("x", x_val))

        frequency_val = float(request.form["frequency"])
        amplitude_val = float(request.form["amplitude"])
        offset_val = float(request.form["offset"])
        sampling_interval_val = float(request.form["sampling_interval"])
        total_time_val = float(request.form["total_time"])
        noise_variance_val = float(request.form["noise_variance"])

        F, H, Q, R, P, x = np.array([[F_val]]), np.array([[H_val]]), np.array([[Q_val]]), np.array([[R_val]]), np.array([[P_val]]), np.array([[x_val]])
        noise_std_dev = np.sqrt(noise_variance_val)
        time_steps = np.arange(0, total_time_val, sampling_interval_val)
        true_signal = offset_val + amplitude_val * np.sin(2 * np.pi * frequency_val * time_steps)
        noisy_signal = [val + np.random.normal(0, noise_std_dev) for val in true_signal]

        # Ініціалізація фільтра Калмана
        kf = KalmanFilter(F, H, Q, R, P, x)

        # Застосування фільтра Калмана
        kalman_estimates = []
        for measurement in noisy_signal:
            kf.predict()
            estimate = kf.update(measurement)
            kalman_estimates.append(estimate[0][0])

        # Графік результатів
        plt.figure(figsize=(10, 5))
        plt.plot(time_steps, noisy_signal, label="Noisy Signal", color="orange", linestyle="-", alpha=0.6)
        plt.plot(time_steps, true_signal, label="True Signal", linestyle="--", color="blue")
        plt.plot(time_steps, kalman_estimates, label="Kalman Filter Estimate", color="green")
        plt.xlabel("Time (s)")
        plt.ylabel("Value")
        plt.title("Kalman Filter Applied to a Noisy Sinusoidal Wave")
        plt.legend()
        plt.grid()

        # Збереження графіка в base64 для HTML
        img = BytesIO()
        plt.savefig(img, format="png")
        img.seek(0)
        plot_url = base64.b64encode(img.getvalue()).decode()
        plt.close()

        return render_template("kalman.html", F_val=F_val, H_val=H_val, Q_val=Q_val, R_val=R_val, P_val=P_val, x_val=x_val, frequency_val=frequency_val, amplitude_val=amplitude_val, offset_val=offset_val, sampling_interval_val=sampling_interval_val, total_time_val=total_time_val, noise_variance_val=noise_variance_val, plot_url=plot_url)

    # Відображення форми без застосування фільтра
    return render_template("kalman.html", F_val=F_val, H_val=H_val, Q_val=Q_val, R_val=R_val, P_val=P_val, x_val=x_val, frequency_val=frequency_val, amplitude_val=amplitude_val, offset_val=offset_val, sampling_interval_val=sampling_interval_val, total_time_val=total_time_val, noise_variance_val=noise_variance_val)


if __name__ == "__main__":
    app.run(debug=True)
```

<p>Результат створення додатку:</p>
<p align="left"> <img src="Screenshots/1.png" alt="Картинка 1"/> </p>

<p><b>Дослідження впливу параметрів:</b></p>
<ol>
        <li>
            <b>Матриця переходу стану (F)</b> визначає, як попередній стан переходить у наступний.
            <p>Збільшення F робить прогнози більш залежними від попередніх станів, що може покращити стійкість до шуму, але уповільнити реакцію на швидкі зміни.</p>
            <p align="left"> <img src="Screenshots/2.png" alt="Картинка 2"/> </p>
            <p>Зменшення F знижує "пам'ять" про попередні стани, що робить фільтр більш чутливим до поточних вимірювань.</p>
            <p align="left"> <img src="Screenshots/3.png" alt="Картинка 3"/> </p>
        </li>
        <li>
            <b>Матриця вимірювання (H)</b> показує фільтру, як вимірювання пов'язані зі станом.
            <p>Збільшення H робить вимірювання більш впливовими на стан, зменшуючи вплив прогнозів.</p>
            <p align="left"> <img src="Screenshots/4.png" alt="Картинка 4"/> </p>
            <p>Зменшення H зменшує залежність фільтра від вимірювань і підсилює довіру до прогнозів.</p>
            <p align="left"> <img src="Screenshots/5.png" alt="Картинка 5"/> </p>
        </li>
        <li>
            <b>Матриця коваріації шуму процесу (Q)</b> визначає ступінь "довіри" фільтра до власних прогнозів.
            <p>Збільшення Q робить фільтр більш чутливим до змін, адже він очікує, що стан змінюватиметься швидше.</p>
            <p align="left"> <img src="Screenshots/6.png" alt="Картинка 6"/> </p>
            <p>Зменшення Q означає, що фільтр більше довірятиме своїм прогнозам і менше орієнтуватиметься на вимірювання.</p>
            <p align="left"> <img src="Screenshots/7.png" alt="Картинка 7"/> </p>
        </li>
        <li>
            <b>Матриця коваріації шуму вимірювання (R)</b> визначає, наскільки фільтр "довіряє" вимірюванням.
            <p>Збільшення R знижує довіру до вимірювань, через що фільтр більше покладатиметься на прогнози.</p>
            <p align="left"> <img src="Screenshots/8.png" alt="Картинка 8"/> </p>
            <p>Зменшення R підвищує довіру до вимірювань, що може поліпшити точність фільтрації за низького рівня шуму, але збільшити похибку при різких змінах.</p>
            <p align="left"> <img src="Screenshots/9.png" alt="Картинка 9"/> </p>
        </li>
        <li>
            <b>Початкова матриця коваріації (P)</b> відображає початкову невпевненість у стані.
            <p>Високі значення P дозволяють фільтру швидше адаптуватися до змін, оскільки він вважає початкову оцінку менш надійною.</p>
            <p align="left"> <img src="Screenshots/10.png" alt="Картинка 10"/> </p>
            <p>Низькі значення P роблять фільтр більш впевненим у початковій оцінці, тому він повільніше адаптуватиметься до змін.</p>
            <p align="left"> <img src="Screenshots/11.png" alt="Картинка 11"/> </p>
        </li>
        <li>
            <b>Початкова оцінка стану (x)</b> може відрізнятися від реального значення.
            <p>Високі значення початкової оцінки стану x змушують фільтр повільніше адаптуватися, оскільки йому потрібно більше коригувань для досягнення реального стану.</p>
            <p align="left"> <img src="Screenshots/12.png" alt="Картинка 12"/> </p>
            <p>Низькі значення x, близькі до реального сигналу, прискорюють адаптацію фільтра, але можуть знижувати його чутливість до змін, якщо початкова оцінка була неточною.</p>
            <p align="left"> <img src="Screenshots/13.png" alt="Картинка 13"/> </p>
        </li>
        <li>
            <b>Частота синусоїдального сигналу (Frequency)</b> визначає, як часто сигнал змінюється впродовж часу.
            <p>Збільшення частоти робить сигнал більш змінним, що перевіряє здатність фільтра адаптуватися до швидких змін.</p>
            <p align="left"> <img src="Screenshots/14.png" alt="Картинка 14"/> </p>
            <p>Зменшення частоти робить сигнал більш плавним, що може спростити завдання фільтра.</p>
            <p align="left"> <img src="Screenshots/15.png" alt="Картинка 15"/> </p>
        </li>
        <li>
            <b>Амплітуда синусоїдального сигналу (Amplitude)</b> визначає максимальне значення синусоїдального сигналу.
            <p>Збільшення амплітуди створює більш різкі коливання, які вимагають більшої точності від фільтра.</p>
             <p align="left"> <img src="Screenshots/16.png" alt="Картинка 16"/> </p>
            <p>Зменшення амплітуди робить сигнал менш змінним, що знижує вимоги до фільтра.</p>
             <p align="left"> <img src="Screenshots/17.png" alt="Картинка 17"/> </p>
        </li>
        <li>
            <b>Постійна складова сигналу (Offset)</b> – зсув сигналу від нуля.
            <p>Збільшує середнє значення сигналу, що перевіряє, наскільки ефективно фільтр може адаптуватися до сигналу, який має постійний зсув відносно нуля.</p>
            <p align="left"> <img src="Screenshots/18.png" alt="Картинка 18"/> </p>
            <p>У випадку, коли offset наближається до нуля, синусоїда коливатиметься навколо нуля, що є типовим тестовим сценарієм для багатьох фільтрів.</p>
            <p align="left"> <img src="Screenshots/19.png" alt="Картинка 19"/> </p>
        </li>
        <li>
            <b>Інтервал дискретизації (Sampling interval)</b> визначає часовий інтервал між вимірюваннями.
            <p>Збільшення інтервалу дискретизації призводить до більш рідкісних вимірювань, що може ускладнити фільтрацію швидкозмінних сигналів.</p>
            <p align="left"> <img src="Screenshots/20.png" alt="Картинка 20"/> </p>
            <p>Зменшення інтервалу дозволяє отримувати більше даних, покращуючи точність, але збільшуючи навантаження на фільтр.</p>
            <p align="left"> <img src="Screenshots/21.png" alt="Картинка 21"/> </p>
        </li>
        <li>
            <b>Загальний час моделювання (Total time)</b> – тривалість симуляції сигналу.
            <p>Збільшення часу дозволяє перевірити, наскільки стабільно фільтр поводиться на тривалих проміжках.</p>
            <p align="left"> <img src="Screenshots/22.png" alt="Картинка 22"/> </p>
            <p>Скорочення часу допомагає дослідити швидкодію фільтра.</p>
            <p align="left"> <img src="Screenshots/23.png" alt="Картинка 23"/> </p>
        </li>
        <li>
            <b>Дисперсія шуму (Noise variance)</b> визначає рівень шуму у сигналі.
            <p>Збільшення дисперсії додає більше шуму, що ускладнює завдання фільтра.</p>
            <p align="left"> <img src="Screenshots/24.png" alt="Картинка 24"/> </p>
            <p>Зменшення дисперсії знижує шум, роблячи фільтрацію простішою.</p>
            <p align="left"> <img src="Screenshots/25.png" alt="Картинка 25"/> </p>
        </li>
</ol>






