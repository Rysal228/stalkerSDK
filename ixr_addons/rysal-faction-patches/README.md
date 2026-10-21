# Rysal Faction Patches для IX-Ray

Отдельный аддон с HD-нашивками Leekos (8 группировок). Трофейная
нашивка появляется в инвентаре обычного погибшего сталкера соответствующей
группировки, если стандартная система выпадения лута NPC активна.

## Как именно аддон связан с движком

* Наличие `addon.init` делает папку модулем IX-Ray; эта папка попадает в
  виртуальную игровую файловую систему через `$arch_dir_addons$`.
* `configs/mod_system_rysal_patches.ltx` — **DLTX к корневому
  `configs/system.ltx`**. Именно туда входят новые предметные секции:
  `rysal_patch_stalker`, `rysal_patch_dolg`, `rysal_patch_freedom`,
  `rysal_patch_bandit`, `rysal_patch_monolith`,
  `rysal_patch_killer`, `rysal_patch_army`, `rysal_patch_ecolog`.
* `configs/misc/mod_death_generic_rysal_patches.ltx` — **DLTX к корневому
  `configs/misc/death_generic.ltx`**. Обновляет три группы таблиц:
  `[stalker]`, `[freedom]` и другие; `[default]` и уровни;
  `[item_count_0]` и остальные сложности. В этих секциях новые нашивки
  назначены своим группировкам, количество 1, шанс 100%.
* Стандартный `death_manager.script` читает `misc/death_generic.ltx`,
  определяет `character_community(npc)` и спавнит предмет с родителем NPC.
* `configs/text/rus/st_rysal_patches.xml` содержит русские названия и
  описания в кодировке **Windows-1251**; сама игра автоматически читает
  обычные таблицы локализации из `configs/text/rus/*.xml`.
* `textures/ui/ui_leekos_patches.dds` — оригинальный чистый атлас из
  `HD Faction Patches 2.4/files/loot/ui_leekos_patches.dds`;
  указатели `icons_texture`, `inv_scale` и `inv_grid_*` индивидуальны.
  Не заменяет `ui_icon_equipment.dds` и не изменяет STCOP.
* Пока модель нашивки на земле `dev_pda.ogf`; она временная.
  В инвентаре отображается иконка из DDS.

## Установка — важно удалить прежние файлы

1. В своей игре открой `ixr_addons/rysal-faction-patches`.
2. **Удали старые четыре LTX**, если они присутствуют:
   ```
   configs/misc/mod_items_rysal_patches.ltx
   configs/misc/mod_death_items_by_communities_rysal_patches.ltx
   configs/misc/mod_death_items_by_levels_rysal_patches.ltx
   configs/misc/mod_death_items_count_rysal_patches.ltx
   ```
3. Забери из этой ветки два **новых** файла:
   ```
   configs/mod_system_rysal_patches.ltx
   configs/misc/mod_death_generic_rysal_patches.ltx
   ```
4. Остальные файлы `addon.init`, `configs/text/rus/st_rysal_patches.xml`
   и `textures/ui/ui_leekos_patches.dds` оставь.
5. Перезапусти игру полностью.

**Причина изменения:** движок DLTX автоматически находит `mod_...ltx`
для корневого LTX. Подключённые `#include` файлы сами по себе не
считаются корневыми и их `mod_...ltx` автоматически не читаются.

## Проверка

Сначала убей обычного свободовца (не сюжетного NPC). После смерти в
инвентаре должна появиться `Нашивка: Свобода`. Затем проверь одиночку
и долговца. Не все специальные NPC генерируют добычу; они могут использовать
`dont_spawn_loot` или особые скрипты.

Если после перезапуска нет предметов, собери из
`_appdata_ixray_/logs/` строки `DLTX`, `rysal_patch`, `death_manager`.
Проверь также наличие `[rysal_patch_freedom]` через меню разработчика
или чтение текущего `system_ini()`, если есть игровая консоль скриптов.

## Правовой статус

Автор Leekos предоставил пакет HD Faction Patches на ModDB с
лицензией Proprietary. Исходный атлас уже загружен пользователем в
публичный репозиторий; отдельный Pull Request его переиспользует без
изменения данных. Для независимого публичного распространения модифицированных
ресурсов требуется проверить разрешения автора.
