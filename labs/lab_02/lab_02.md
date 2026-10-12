---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.19.5
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- #region id="7247b8d4" -->
# Лабораторная работа 2. Пакет Matplotlib

Продолжительность работы: - 4 часа.

Мягкий дедлайн (100 баллов): 09.10.2026

Жесткий дедлайн (50 баллов): 16.10.2026


Оценка за каждое задание указана в комментариях перед заданием.

Задание выполняется самостоятельно, в противном случае все причастные получат 0 баллов :)
Если вы нашли решение любого из заданий (или его части) в открытом источнике, необходимо указать ссылку на этот источник в отдельном блоке в конце своей работы. 
В противном случае **работа также будет оценена в 0 баллов**.
<!-- #endregion -->

### Задание 1 (1 балл)

Сгенерируйте массив нормально распределенных значений размерности 2 из 100 точек (выберите среднее значение $\mu$ и среднее квадратическое отклонение $\sigma$ по своему выбору). 
Проверьте [правило трех сигм](https://ru.wikipedia.org/wiki/%D0%A1%D1%80%D0%B5%D0%B4%D0%BD%D0%B5%D0%BA%D0%B2%D0%B0%D0%B4%D1%80%D0%B0%D1%82%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%BE%D0%B5_%D0%BE%D1%82%D0%BA%D0%BB%D0%BE%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5#%D0%9F%D1%80%D0%B0%D0%B2%D0%B8%D0%BB%D0%BE_%D1%82%D1%80%D1%91%D1%85_%D1%81%D0%B8%D0%B3%D0%BC): нарисуйте окружность с центром в точке $\mu$ с таким радиусом, чтобы на нее приходилось 0,99 всех точек, а также окружность радиусом 3 сигмы. 
Выделите точку $\mu$ отдельным цветом.

```python
# ваш код должен быть тут
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng()

mu = np.array([0.0, 0.0])
sigma = 1.5

points = rng.normal(loc=mu, scale=sigma, size=(100, 2))

dist = np.linalg.norm(points - mu, axis=1)

r99 = np.quantile(dist, 0.99)
r3s = 3 * sigma

fig, ax = plt.subplots(figsize=(7, 7))
ax.scatter(points[:, 0], points[:, 1], alpha=0.6, label='точки')
ax.scatter(*mu, color='red', s=120, marker='X', zorder=5, label=r'$\mu$')

theta = np.linspace(0, 2*np.pi, 400)
ax.plot(mu[0] + r99*np.cos(theta), mu[1] + r99*np.sin(theta),
        color='green', label=f'r(0.99) = {r99:.2f}')
ax.plot(mu[0] + r3s*np.cos(theta), mu[1] + r3s*np.sin(theta),
        color='orange', linestyle='--', label=f'3σ = {r3s:.2f}')

ax.set_aspect('equal')
ax.legend()
ax.set_title('Правило трёх сигм')
plt.show()
```

## Задание 2 

Используйте вспомогательный график, чтобы нарисовать гистограммы с 10 сегментами для каждого измерения и построить график плотности вдоль гистограммы для данных из первого задания.

```python executionInfo={"elapsed": 316, "status": "ok", "timestamp": 1694439806964, "user": {"displayName": "Sergey Korpachev", "userId": "09181340988160569540"}, "user_tz": -180} id="8f1ca529"
# ваш код должен быть тут
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

for i, ax in enumerate(axes):
    data = points[:, i]
    counts, bins = np.histogram(data, bins=10, density=True)
    centers = (bins[:-1] + bins[1:]) / 2
    width = bins[1] - bins[0]

    ax.bar(centers, counts, width=width, alpha=0.6, color='steelblue',
           edgecolor='black', label='гистограмма')

    xs = np.linspace(data.min(), data.max(), 200)
    pdf = (1 / (sigma * np.sqrt(2*np.pi))) * np.exp(-((xs - mu[i])**2) / (2*sigma**2))
    ax.plot(xs, pdf, color='red', linewidth=2, label='плотность N(μ, σ²)')

    ax.set_title(f'Измерение {i+1}')
    ax.set_xlabel('значение')
    ax.set_ylabel('плотность')
    ax.legend()

plt.tight_layout()
plt.show()
```

<!-- #region id="6e8398b0" -->

### Задание  3 

Загрузите набор данных ["Ирисы Фишера"](https://ru.wikipedia.org/wiki/Ирисы_Фишера). 
Создайте тепловую карту с корреляциями между признаками объектов, строки и столбцы которой должны быть подписаны названиями признаков. 
Важно использовать matplotlib. 
Прямая корреляция должна отображаться зеленым цветом, обратная &mdash; красным, а отсутствие корреляции &mdash; белым. 
Сделайте график достаточно крупным.

**Подсказка:** используйте `plt.xticks`, `plt.yticks`, `plt.imshow`, `plt.colorbar`


<!-- #endregion -->

```python id="b6414734"
# ваш код должен быть тут
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

for i, ax in enumerate(axes):
    data = points[:, i]
    counts, bins = np.histogram(data, bins=10, density=True)
    centers = (bins[:-1] + bins[1:]) / 2
    width = bins[1] - bins[0]

    ax.bar(centers, counts, width=width, alpha=0.6, color='steelblue',
           edgecolor='black', label='гистограмма')

    xs = np.linspace(data.min(), data.max(), 200)
    pdf = (1 / (sigma * np.sqrt(2*np.pi))) * np.exp(-((xs - mu[i])**2) / (2*sigma**2))
    ax.plot(xs, pdf, color='red', linewidth=2, label='плотность N(μ, σ²)')

    ax.set_title(f'Измерение {i+1}')
    ax.set_xlabel('значение')
    ax.set_ylabel('плотность')
    ax.legend()

plt.tight_layout()
plt.show()
```

## Задание 4

Cоздайте такую же тепловую карту, используя `seaborn.heatmap`

```python executionInfo={"elapsed": 293, "status": "ok", "timestamp": 1694439821851, "user": {"displayName": "Sergey Korpachev", "userId": "09181340988160569540"}, "user_tz": -180} id="47f49efd"
# ваш код должен быть тут
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.colors import LinearSegmentedColormap

url = 'https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv'
df = pd.read_csv(url)

feature_names = ['sepal_length', 'sepal_width', 'petal_length', 'petal_width']
X = df[feature_names].values

corr = np.corrcoef(X, rowvar=False)

fig, ax = plt.subplots(figsize=(9, 9))

cmap = LinearSegmentedColormap.from_list('rg', ['red', 'white', 'green'])

im = ax.imshow(corr, cmap=cmap, vmin=-1, vmax=1)

ax.set_xticks(range(len(feature_names)))
ax.set_yticks(range(len(feature_names)))
ax.set_xticklabels(feature_names, rotation=45, ha='right')
ax.set_yticklabels(feature_names)

for i in range(len(feature_names)):
    for j in range(len(feature_names)):
        ax.text(j, i, f'{corr[i, j]:.2f}',
                ha='center', va='center', color='black', fontsize=11)

plt.colorbar(im, ax=ax)

ax.set_title('Корреляции признаков Ирисов Фишера')
plt.tight_layout()
plt.show()
```
