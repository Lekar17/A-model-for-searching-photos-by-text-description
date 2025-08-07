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

Изображений: ResNet-18 (без последнего FC-слоя) → 513-мерный вектор

Текстов: all-MiniLM-L6-v2 (sentence-transformers) → 384-мерный вектор

Формирование обучающего датасета
• Конкатенация текстовых и визуальных векторов → 899 признаков
• Целевая переменная — нормализованная экспертная оценка (expert_score)

Train/Test-split
• GroupShuffleSplit по image_id (7:3) — исключаем утечку картинок в обе выборки

Обучение и оценка моделей
• DummyRegressor (baseline)
• LinearRegression (через масштабирование признаков)
• RandomForestRegressor (base)
• GradientBoostingRegressor
• Полносвязная нейронная сеть прямого распространения
• CLIP модель нейросети
Метрики: MSE, RMSE, MAE

Выбор лучшей модели
• RandomForestRegressor

MSE = 0.0614

Тестирование
• Сбор эмбеддингов тестовых изображений
• Функция для проверки запрещенных слов
• Загрузка тестовой выборки
• При наличии «запрещённых» слов показываем дисклеймер

Структура репозитория
├── README.md               — это описание  
├── requirements.txt        — зависимости Python  
├── notebook.ipynb          — Jupyter-тетрадь с полным пайплайном  
├── train_dataset.csv       — пары <image, query_id, query_text>  
├── CrowdAnnotations.tsv    — оценки краудсорса  
├── ExpertAnnotations.tsv   — оценки экспертов  
├── train_images/           — папка с обучающими изображениями  
├── test_queries.csv        — тестовые запросы  test_images/            
└── папка с тестовыми изображениями  
