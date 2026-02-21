# AI Trend Analysis Agent (n8n + LLM)

Production-ready AI-агент для автоматического анализа трендов на основе текстовых источников (новости / соцсети), реализованный в n8n.
Агент извлекает сигналы из сырых данных, проводит временной анализ, классифицирует тренды по динамике и устойчивости, 
ограничивает количество значимых сигналов и формирует краткие аналитические интерпретации с помощью LLM.

---

<img width="1674" height="457" alt="Workflow Screenshot AI-Trend-Analysis-Agent-n8n" src="https://github.com/user-attachments/assets/4b93c04e-6ed6-4eeb-816a-c9d861ab8c99" />


---

## Основные возможности

- Нормализация данных из внешних источников (News API / соцсети);
- Извлечение смысловых сигналов (bigrams);
- Time-series анализ (сравнение периодов);
- Классификация трендов (trend / spike / noise);
- Оценка устойчивости тренда (emerging / stable / volatile);
- Ограничение top-N трендов для экономии токенов;
- Интерпретация трендов с помощью LLM;
- Защита от нестабильного вывода моделей (post-filter);
- Готовность к интеграции с Telegram / Notion / отчетами.

---

## Архитектура пайплайна
```
Trigger (Cron)
→ Config
→ Fetch Raw Data (News API / Social)
→ Normalize Data
→ Extract Signals (bigrams only)
→ Time-series Analysis
→ Trend Detection
→ IF (is_trend)
→ Trend Stability
→ IF (is_meaningful)
→ Top N Trends
→ LLM Interpretation
→ Post-Filter
→ Output (Telegram Send message / Notion Report)
Error trigger workflow
```
---

## Логика анализа трендов

Метрики:
- current_mentions;
- previous_mentions;
- growth_ratio;
- velocity.

Классификация:
trend — устойчивый рост;
spike — резкий всплеск;
noise — статистический шум.

Устойчивость:
emerging — новый формирующийся тренд;
stable — подтвержденный тренд;
volatile — краткосрочный всплеск;
unknown — недостаточно данных.

---

## Использование LLM

1. LLM вызывается только для top-N трендов;
2. Один вызов = один аналитический батч;
3. Строгий system + user prompt;
4. Ограничение длины ответа;
5. Post-filter для защиты от runaway generation.

---

## Обработка ошибок и наблюдаемость

В пайплайне реализована централизованная обработка ошибок с использованием `Error Trigger` в n8n.

Задачи блока Error Handling:
- перехват любых ошибок выполнения workflow;
- предотвращение «тихих» падений сценариев;
- оперативное уведомление о сбоях;
- упрощение отладки и мониторинга.

Архитектура обработки ошибок:
Error Trigger → Format Error (Code) → Telegram: Send Message

## Стек
```
n8n
JavaScript (Code nodes)
OpenRouter / LLM APIs
News API
Cron
Telegram Bot API 
Notion
```

## Ограничения
Качество анализа зависит от источников данных
Бесплатные LLM-модели могут быть нестабильны
Для продакшн-использования рекомендуется платный LLM
