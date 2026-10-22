# Rysal Quests — все наши задания в одном аддоне

Для **IX-Ray 1.3.4 / STCOP 3.8**. Новые квесты живут в одном каталоге
`ixr_addons/rysal-quests/`, без дублирования в оригинальной `gamedata`.

**Сыч:** шесть цикличных заказов на нашивки (случайная выплата).
Нужен отдельный установленный `rysal-faction-patches`.

**Борода → Гаваец → Борода:** принести пять банок тушёнки за 500 ₽,
через 24 игровых часа обсудить поставки, заплатить Гавайцу на
«Янове» 2000 ₽, доложить Бороде.

## Структура

```
ixr_addons/
  rysal-faction-patches/             # предметы и их выпадение
  rysal-quests/                      # наши квесты
    addon.init
    configs/
      mod_system_rysal_quests.ltx
      gameplay/
        mod_character_desc_zaton_owl_patches.xml
        rysal_owl_patch_dialogs.xml
        rysal_owl_patch_info.xml
        rysal_beard_supply_dialogs.xml
        rysal_beard_supply_info.xml
      misc/mod_task_manager_rysal_quests.ltx
      text/rus/
        st_rysal_owl_patch_quests.xml
        st_rysal_beard_quests.xml
    scripts/
      rysal_owl_patch_quests.script
      demo_conserva_quest.script
```

Обе линии зарегистрированы в одном корневом DLTX `system.ltx`,
а задания — в одном DLTX к `misc/task_manager.ltx`.
Lua и локализация цепочки Бороды перенесены **без изменений**:
ID `demo_*` и ключ сохранённого игрового времени
`demo_conserva_done_game_time` сохранены, чтобы не сбрасывать прогресс.

## Ограничение XMLOverride IX-Ray 1.3.4

XMLOverride при `override="add"` на `specific_character` в этой
версии выбирает элемент по имени тега и **не проверяет его `id`**.
Для Сыча это работает (он первый в XML Затона). Но Борода и Гаваец
не первые в своих файлах: такой патч может приписать их диалоги
постороннему NPC.

Поэтому пока **только пять ссылок на диалоги** остаются в действующих
STCOP-профилях. В
`ixr_addons/ixray-stcop-wp-3.8-cop/configs/gameplay/character_desc_zaton.xml`,
персонаж `zat_a2_stalker_barmen` (Борода):

```xml
<actor_dialog>demo_conserva_offer</actor_dialog>
<actor_dialog>demo_conserva_turn_in</actor_dialog>
<actor_dialog>demo_supplies_offer</actor_dialog>
<actor_dialog>demo_supplies_report</actor_dialog>
```

В том же STCOP-аддоне в `configs/gameplay/character_desc_jupiter.xml`,
персонаж `jup_a6_stalker_barmen` (Гаваец):

```xml
<actor_dialog>demo_supplies_hawaiian</actor_dialog>
```

Если добавлялись ранее — **не дублируй**. Если отсутствуют, добавь
только недостающие строки после резервного копирования этих двух
файлов. При переустановке STCOP потребуется восстановить эти ссылки.
Все остальные файлы игрового квестового контента теперь автономные.

## Переход со старой версии

1. Сделай резервную копию сохранения и своей текущей `gamedata`.
2. Удали **весь старый аддон**
   `ixr_addons/rysal-owl-patch-quests/`: иначе те же ID Сыча
   зарегистрируются дважды.
3. Скопируй `ixr_addons/rysal-quests/` целиком в папку аддонов.
4. **Очисти старые `demo_*` записи в установленной `gamedata`**.
   В этом PR уже удалены только наши вставки из восьми файлов:
   - `gameplay/character_desc_zaton.xml`, `character_desc_jupiter.xml`;
   - `gameplay/dialogs_zaton.xml`, `dialogs_jupiter.xml`, `info_zaton.xml`;
   - `misc/tm_zaton.ltx`;
   - `text/rus/st_dialogs_zaton.xml`, `st_quests_zaton.xml`.
   Можно скопировать очищенные версии из обновлённого репозитория,
   **но только если остальные моды не меняли эти большие файлы**.
   Иначе удаляй из своей копии лишь блоки с ID `demo_*`.
5. Удали `gamedata/scripts/demo_conserva_quest.script`. Он теперь
   в `rysal-quests/scripts`.
6. Проверь пять строк в двух STCOP-файлах, полностью перезапусти игру.

**Важно:** не оставляй старые определения диалогов или задач в
`gamedata` одновременно с новой папкой — возможны конфликты
одинаковых ID. И не удаляй работающие привязки Бороды/Гавайца из STCOP.

## Тест

- Сыч: взять контракт на нашивки, сдать пятёрку, получить случайную
  выплату, взять повторно.
- Борода: взять заказ, сдать ровно пять банок (500 ₽).
- Через 24 игровых часа: к Гавайцу с 2000 ₽, затем доклад Бороде.
- Загрузить существующие сохранения на разных этапах цепочки,
  проверить появление КПК-задач и отсутствие повторной выдачи денег.

**Не тестировалось в самой игре после переноса.** Проверяются те же
ID и функции, но новая упаковка/регистрация нуждаются в игровом тесте.
