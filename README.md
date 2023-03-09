# Интреграция Roidmi Eva в Home Assistant
### **Необходимые HACS-интеграции:**
- [Yandex Station](https://github.com/AlexxIT/YandexStation)
- [Yandex Smart Home](https://github.com/dext0r/yandex_smart_home)
- [Xiaomi Miot Auto](https://github.com/al-one/hass-xiaomi-miot)
- [Xiaomi Cloud Map Extractor](https://github.com/PiotrMachowski/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor)

## Ознакомление
Для начала вам необходимо ознакомиться с данными, что нам понадобятся:
- `mapId` - ID вашей карты, он для каждого уникален. Вытащить можно из логов [Mi Home Vevs](https://www.vevs.me/2017/11/mi-home.html)
- `ID комнат` - ID комнат на вашей карте. Вытащить можно также из логов [Mi Home Vevs](https://www.vevs.me/2017/11/mi-home.html), а также с помощью [Xiaomi Cloud Map Extractor](https://github.com/PiotrMachowski/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor)

Пункты [Интеграция](#integration) и [Добавление карточки на главную панель](#add_vacuum_card) лучше выполнять параллельно.

P.S. В пункте [Добавление карточки на главную панель](#add_vacuum_card) можно подсмотреть ID каждой комнаты.
## <a name="integration"></a>[Интеграция](#integration)
1. Настраиваем все, согласно инструкциям каждого необходимого пакета
2. Добавляем [скрипт уборки комнаты](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Falternativniy%2Fha_roidmi_eva%2Fblob%2Fmain%2Fblueprints%2Fscript%2Fvacuum_eva%2Fclean_room.yaml) и настраиваем его. Советую сразу задать собственный id скрипта.

    [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Falternativniy%2Fha_roidmi_eva%2Fblob%2Fmain%2Fblueprints%2Fscript%2Fvacuum_eva%2Fclean_room.yaml)
3. Добавляем [автоматизацию для команд Алисы](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Falternativniy%2Fha_roidmi_eva%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fvacuum_eva%2Fyandex_station_commands.yaml) и настраиваем ее, привязав к ней ранее созданный скрипт

    [![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Falternativniy%2Fha_roidmi_eva%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fvacuum_eva%2Fyandex_station_commands.yaml)
4. Готово. Ваша Алиса умеет работать с комнатами!

## <a name="add_vacuum_card"></a>[Добавление карточки на главную панель](#add_vacuum_card)
1. Ставим кастомный пользовательский интерфейс [Lovelace Vacuum Map card](https://github.com/PiotrMachowski/lovelace-xiaomi-vacuum-map-card) через HACS
2. Добавляем карточку ***Custom: Xiaomi Vacuum Map Card*** на главную панель
3. В пункте ***платформа пылесоса*** выбираем `al-one/hass-xiaomi-miot`
4. Поочередно жмем кнопки ***Generate static config*** и ***Generate rooms config*** пока у нас не появятся комнаты на карте
5. Далее нужно добавить вызов запуска уборки выбранной комнаты. Идем в текстовый редактор и редактируем:
    ``` yaml
    ...

    map_modes:
    - name: Rooms
      icon: mdi:floor-plan
      run_immediately: false
      coordinates_rounding: true
      selection_type: ROOM
      max_selections: 1
      repeats_type: EXTERNAL
      max_repeats: 3

      # ВАЖНАЯ ЧАСТЬ
      service_call_schema:
        service: script.your_script_clean_room #id-скрипта уборки комнат
        service_data:
          rooms: '[[selection]]'
      # ВАЖНАЯ ЧАСТЬ

      ...
    ```
6. Готово. Теперь у вас есть карточка с рабочей покомнатной уборкой.