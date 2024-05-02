# Content
- templates
  - config.json - пример конфига в json
  - config.yaml - пример конфига в yaml
  - sleep_default.json - пример шаблона дефолтного
  - sleep_type_1.json - пример других шаблонов, это все описано в конфиге и там есть интересный момент с conditions
- sleep_dept.json - заготовка на будущий другой график
- sleep_performance.json - заготовка на будущий другой график
- sleep_time_chart.json - ответ апишки с данными по графику из нашего шаблона

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
    "text": "Let’s aim for a full {min_sleep_time}-{max_sleep_time} hours tonight to help you feel more refreshed and ready to tackle the day."
}
```

# Conditions

Представим, что у нас есть N шаблонов, из которых нужно выбрать один для отображения деталки. Будем делать это на основе параметров из контекста (профиль юзера, данные текущего контекста (сон, тренировка и тд))

## JSON Logic
Есть вариант использовать инструмент JSON Logic https://jsonlogic.com/

Преимущества:
- инструмент и формат давно известен и chartGPT умеет писать на нем, т.е. AI-генерируемый
- он похож на то, что у нас уже реализовано
- есть UI билдеры для сборк условий через UI, а на выходе json (или можно написать свой)
- есть либы под JS, Python, C++, Java

Недостатки:
- несколько громоздкий изза json 
- нет либы под iOS, но скорее всего сгодится JS тк шаблон можно процессить js'ом

### Пример использования в конфиге
Есть два шаблона
- sleep_bad.json
- sleep_good.json

Все шаблоны по сну описаны в конфиге config.json, который содержит правила выбора шаблона.

Пример конфига
```json
{
  "type": "sleep",
  "templates": {
    "default": {
      "url": "https://raw.githubusercontent.com/sh0ked/historical-chart-data/main/templates/sleep_default.json",
      "path": "templates/sleep_default.json"
    },
    "type_1": {
      "url": "https://raw.githubusercontent.com/sh0ked/historical-chart-data/main/templates/sleep_type_1.json",
      "path": "templates/sleep_type_1.json",
      "conditions": {
        "and": [
          {
            "<": [{"var": "sleep_time"}, 25200]  // спал меньше 7 часов
          },
          {
            "==": [{"var": "sleep_ranges.deep"}, 5430]  // время глубокого сна равна 89 секунд
          },
          {
            "==": [{"var": "sleep_interp.deep"}, "bad"]  // название качества такого сна равно bad :)
          }
        ]
      }
    }
  }
}
```

Пример данных для условий

```json
{ "sleep_time" : 20000, "sleep_ranges" : { "deep" : 5430 }, "sleep_interp" : { "deep" : "bad" } }
```

### Пример использования в конфиге
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


## Наши условия из visible conditions

Пример описания в конфиге
```json
{
    "$and": [
        {
            "geo_country": "Ukraine"
        },
        {
            "geo_country": "Israel"
        }
    ]
}
```