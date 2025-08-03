Image-Text Retrieval Prototype
Описание
Прототип системы поиска фотографий по текстовым описаниям. Решает задачу измерения соответствия «картинка ↔ текст» через объединённые векторы признаков и регрессию экспертных оценок.

Функциональные блоки
Загрузка и предобработка данных
• train_dataset.csv, CrowdAnnotations.tsv, ExpertAnnotations.tsv, test_queries.csv, папки train_images/, test_images/
• Проверка на пропуски и дубли

Фильтрация «вредного» контента
• Список запрещённых слов (child, children, kid, toddler, infant, baby, minor, underage)
• Отбрасываем все записи с такими описаниями

Векторизация

Изображений: ResNet-50 (pretrained на ImageNet, без выходного слоя, GlobalAveragePooling) → 2048-мерный вектор

Текстов: all-MiniLM-L6-v2 (sentence-transformers) → 384-мерный вектор

Формирование обучающего датасета
• Конкатенация текстовых и визуальных векторов → 2432 признака
• Целевая переменная — нормализованная экспертная оценка (expert_score)

Train/Test-split
• GroupShuffleSplit по image_id (7:3) — исключаем утечку картинок в обе выборки

Обучение и оценка моделей
• DummyRegressor (baseline)
• LinearRegression
• RandomForestRegressor (base + GridSearchCV)
• GradientBoostingRegressor
• MLPRegressor (разные гиперпараметры)
Метрики: MSE, RMSE, MAE, R², Cosine-Similarity

Выбор лучшей модели
• Tuned RandomForestRegressor (n_estimators=200, max_depth=None)

MSE = 0.0854, RMSE = 0.2922, MAE = 0.2305

R² = 0.0596, Cosine = 0.4206

Тестирование
• Собираем эмбеддинги для 100 тестовых изображений
• Генерируем векторы для всех тест-запросов (500 → уникальных 100)
• Для 10 случайных запросов наглядно выводим картинку с наибольшим предсказанным скором
• При наличии «запрещённых» слов показываем дисклеймер

Структура репозитория
├── README.md               — это описание  
├── requirements.txt        — зависимости Python  
├── notebook.ipynb          — Jupyter-тетрадь с полным пайплайном  
├── train_dataset.csv       — пары <image, query_id, query_text>  
├── CrowdAnnotations.tsv    — оценки краудсорса  
├── ExpertAnnotations.tsv   — оценки экспертов  
├── train_images/           — папка с обучающими изображениями  
├── test_queries.csv        — тестовые запросы  
├── test_images/            — папка с тестовыми изображениями  
└── utils/                  — вспомогательные модули (фильтрация, векторизация)
