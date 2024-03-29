# Home Assistant Icelandic weather cards
Packages for [Home Assistant] with Icelandic weather providers from local Icelandic sources.

## Requirements
Working setup of [Home Assistant] and access to [configuration.yaml]

## Installing
Go to the releases page and download the latest release and unzip to the root folder of your [Home Assistant] samba folder. Edit `config/configuration.yaml` and add the following to the `homeassistant` section to include all packages:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

or you can include only specific packages:

```yaml
homeassistant:
  packages:
    vedur_is: !include packages/vedur_is.yaml
    vegagerdin: !include packages/vegagerdin.yaml
    belgingur: !include packages/belgingur.yaml
```
Then restart Home Assistant.

## Available weather cards
- [Veður.is](#vedur)
- [Belgingur](#belgingur)
- [Vegagerðin](#vegagerdin)

## <a name="vedur"></a>Veður.is (vedur_is.yaml)
![vedur_is](docs/vedur_is.png)

Weather information from vedur.is. The weather card is named `weather.vedur_is`. Just add this as any other normal weather card. The integration calls XML services as specified in [Gagnaveita - XML þjónusta] every 10 minutes for temperature data and every hour for forecast data. The XML data is cached locally in folder /config/www/vedur_is.

### Customizing what weather station to use for vedur.is
Default weather station is Reykjavík with id=1. You can find out other ids by checking [Veðurstöðvar]. Click on `Upplýsingar` and find out the `Stöðvanúmer`. You can then go to `Developer tools` and find the entity `input_number.vedur_vedurstod` and change the state to the selected id number or simply add this entity to a dashboard and modify the station id directly there.

### Display text weather description in a markdown card.
You can display vedur.is weather description for the capital Reykjavik and the whole country in a markdown card by adding a mark down card and specifying for example this content:

```markdown
**<ha-icon icon="mdi:weather-cloudy"></ha-icon> {{ states('sensor.vedur_text_capital') }}** 
{{ state_attr('sensor.vedur_text_capital','text') }}

**<ha-icon icon="mdi:weather-cloudy"></ha-icon> {{ states('sensor.vedur_text_country') }}**  
{{ state_attr('sensor.vedur_text_country','text') }}

[Veðurspá](https://www.vedur.is/vedur/spar/thattaspar/hofudborgarsvaedid/#teg=urkoma)
```

### Display aurora forecast.
Aurora forecast for next evening is available in entity `sensor.vedur_aurora` as a value from 0..9 for aurora activity and as a text value in entity `sensor.vedur_aurora_text`. To display the aurora value as a gauge like this:

![aurora_gauge](docs/aurora_gauge.png)

You can use this yaml code:
```yaml
type: gauge
entity: sensor.vedur_aurora
min: 0
max: 9
needle: true
segments:
  - from: 0
    color: DodgerBlue
    label: Lágmarksvirkni
  - from: 1
    color: DodgerBlue
    label: Mjög lítil virkni
  - from: 2
    color: SeaGreen
    label: Lítil virkni
  - from: 3
    color: SeaGreen
    label: Dálítil virkni
  - from: 4
    color: Orange
    label: Talsverð virkni
  - from: 5
    color: Orange
    label: Allmikil virkni
  - from: 6
    color: Crimson
    label: Mikil virkni
  - from: 7
    color: Crimson
    label: Öflug virkni
  - from: 8
    color: DarkRed
    label: Mjög öflug virkni
  - from: 9
    color: DarkRed
    label: Hámarksvirkni
```

There is also aurora forecast for the next days displayed as a table. This can be displayed in markdown card with:

```markdown
{{ state_attr('sensor.vedur_aurora','forecast') }}
```

## <a name="belgingur"></a>Belgingur (belgingur.yaml)
![belgingur](docs/belgingur.png)

Weather forecast information from [belgingur.is]. The weather card is named `weather.belgingur`. Just add this as any other normal weather card. The card uses your current Home position for weather location as specified in General configuration. The information is updated every 10 minutes.

## <a name="vegagerdin"></a>Vegagerðin (vegagerdin.yaml)
![vegagerdin](docs/vegagerdin.png)

Weather information from vegagerdin.is. The weather card is named `weather.vegagerdin`. This card used 
[Gagnaveita Vegagerðarinnar] to retrieve weather information. This only includes current temperature and other measurements but no description or weather forecast. The data updates every 10 minutes.

### Customizing weather station to use for vegagerðin
Default weather station is `Reykjavík`. You can find out other names by checking [Gagnaveita] and finding your station `Nafn` in the list. You can then go to `Developer tools` and find the entity `input_text.vegagerdin_vedurstod` and change the value or simply add this entity to a dashboard and modify the station name directly there.

## Custom weather card
You can either use the included weather cards directly as is or create your own custom weather card using information from different sources, by adding something like this in your `configuration.yaml` which integrates sensors from all the different sources.

```yaml
weather:
  - platform: template
    name: "My custom weather"
    attribution_template: "Veður.is/Vegagerðin/Belgingur"
    condition_template: "{{ states('sensor.vedur_condition') }}"
    temperature_template: "{{ states('sensor.vegagerdin_temperature') }}"
    humidity_template: "{{ states('sensor.vedur_humidity') }}"
    pressure_template: "{{ states('sensor.vedur_pressure') }}"
    wind_speed_template: "{{ states('sensor.belgingur_wind_speed') }}"
    wind_bearing_template: "{{ states('sensor.belgingur_wind_bearing') }}"
    visibility_template: "{{ states('sensor.vedur_visibility') }}"
    forecast_template: "{{ state_attr('sensor.vedur_forecast','forecast') }}"
```

[Home Assistant]: https://www.home-assistant.io
[Gagnaveita - XML þjónusta]: https://www.vedur.is/um-vi/vefurinn/xml
[Veðurstöðvar]: https://www.vedur.is/vedur/stodvar
[belgingur.is]: https://belgingur.is
[Gagnaveita Vegagerðarinnar]: http://www.vegagerdin.is/upplysingar-og-utgafa/gagnaveita-vegagerdarinnar
[Gagnaveita]: http://gagnaveita.vegagerdin.is/api/vedur2014_1
[PayPal]: https://paypal.me/kristjanbjarni
[configuration.yaml]: https://www.home-assistant.io/docs/configuration/
