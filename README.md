# Components
## График
```json
{
	"type": "heart_rate_chart",
  "period": "P1D"
}
```

## Text & Placeholders

```json
{
	"type": "text.body",
  "text": "Let’s aim for a full $min_sleep_time$-$max_sleep_time$ hours tonight to help you feel more refreshed and ready to tackle the day."
}
```

# Conditions

Представим, что у нас есть N шаблонов, в которых нужны условия для выбора тех или иных блоков для отображения. Будем делать это на основе параметров из контекста (профиль юзера, данные текущего контекста (сон, тренировка и тд))

## JSON Logic
Есть вариант использовать инструмент JSON Logic https://jsonlogic.com/

Преимущества:
- инструмент и формат давно известен и chartGPT умеет писать на нем, т.е. AI-генерируемый
- он похож на то, что у нас уже реализовано
- есть UI билдеры для сборк условий через UI, а на выходе json (или можно написать свой)
- есть либы под JS, Python, C++, Java

Недостатки:
- несколько громоздкий изза json 
- нет либы под iOS, но скорее всего достаточно JS тк шаблон можно процессить js'ом

### Пример данных для условий

```json
{ "sleep_time" : 20000, "sleep_ranges" : { "deep" : 5430 }, "sleep_interp" : { "deep" : "bad" } }
```

### Пример использования в шаблоне
```json
{
	"content": [
		{
			"type": "vbox",
			"items": [
				{
					"text": "Header",
					"type": "text.header",
          "conditions": {
						"and": [
							{
								"<": [{"var": "sleep_time"}, 25200]
							},
							{
								"==": [{"var": "sleep_ranges.deep"}, 5430]
							},
							{
								"==": [{"var": "sleep_interp.deep"}, "bad"]
							}
						]
					}
				},
				{
					"text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. https://welltory.com",
					"type": "text.body"
				},
```
