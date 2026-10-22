# Заказы Сыча на нашивки (IX-Ray 1.3.4 / CoP + STCOP)

Аддон `rysal-owl-patch-quests` предоставляет Сычу шесть цикличных заказов:
пять трофейных нашивок одной группировки за вознаграждение. **Зависимость**:
установленный аддон `rysal-faction-patches` (который создаёт секции
`rysal_patch_*`).

| Группировка | Нашивок | Награда |
|---|---:|---:|
| Бандиты | 5 | 2 000 ₽ |
| Одиночки | 5 | 3 000 ₽ |
| Свобода | 5 | 4 000 ₽ |
| Долг | 5 | 5 000 ₽ |
| Наёмники | 5 | 7 000 ₽ |
| Монолит | 5 | 10 000 ₽ |

Заказы независимы, могут выполняться одновременно. После сдачи
пяти нашивок и завершения задачи в КПК игрок может вновь взять тот же
заказ. Пять нашивок изымаются при сдаче, награда начисляется штатным
`task_manager` ровно один раз на цикл.

## Важное исправление для сборки b7f33238c (IX-Ray 1.3.4)

У этой версии **нет поддержки диалогов от инфопоршней актёра** в
`CActor::UpdateAvailableDialogs`. Она перечисляет только
`CharacterInfo().ActorDialogs()` собеседника. Поэтому предыдущие
версии аддона на основе `rysal_owl_patch_dialogs_enabled` не могли
показать Сычу новую реплику. Не нужно добавлять эту инфопоршню
вручную и не нужно запускать старый `rysal_owl_patch_init.script`.

В этой версии используется корректный **XMLOverride в профиль NPC**:
`configs/gameplay/mod_character_desc_zaton_owl_patches.xml`:

```xml
<xml>
    <specific_character id="zat_b30_owl_stalker_trader" override="add">
        <actor_dialog>rysal_owl_patch_offer</actor_dialog>
        <actor_dialog>rysal_owl_patch_turn_in</actor_dialog>
    </specific_character>
</xml>
```

В IX-Ray 1.3.4 атрибут `override="add"` на
`specific_character` добавляет именно **его дочерние элементы**.
Ранее атрибут стоял на `actor_dialog`, который не имел потомков,
поэтому не добавлял ничего.

**Ограничение XMLOverride старой версии:** при выборе
`specific_character` алгоритм ориентируется на имя XML-тега,
а не на атрибут `id`. В проверенных штатном `character_desc_zaton.xml`
и профиле STCOP, который прислал пользователь, Сыч стоит первым;
это позволяет добавить реплики именно ему. Для иных версий STCOP,
изменивших порядок персонажей, потребуется отдельная проверка.

## Интеграция

- `configs/gameplay/rysal_owl_patch_dialogs.xml`: реестр диалогов;
- `configs/gameplay/rysal_owl_patch_info.xml`: инфопоршни заказов;
- `configs/mod_system_rysal_owl_xml.ltx`: DLTX, добавляющий файлы
  в `[dialogs] files` и `[info_portions] files` корневого `system.ltx`;
- `configs/misc/mod_task_manager_owl_patches.ltx`: DLTX задания КПК;
- `configs/text/rus/st_rysal_owl_patch_quests.xml`: русские строки в CP1251;
- `scripts/rysal_owl_patch_quests.script`: Lua с проверкой нашивок и
  цикличностью квестов;
- `configs/gameplay/mod_character_desc_zaton_owl_patches.xml`:
  **две ссылки на диалоги в профиле Сыча через XMLOverride**.

Штатные файлы STCOP, Бороды и исходной `gamedata` не меняются.

## Обновление из старых версий

1. Сделай резервную копию сохранения и своего STCOP-профиля.
2. **Полностью удали** старую игровую папку
   `ixr_addons/rysal-owl-patch-quests/`. Это уберёт старые
   `mod_dialogs_zaton_owl_patches.xml`,
   `mod_info_zaton_owl_patches.xml` и
   `rysal_owl_patch_init.script`.
3. Скопируй свежую папку аддона из текущего PR в `ixr_addons/`
   рядом с `rysal-faction-patches`.
4. В `ixr_addons/ixray-stcop-wp-3.8-cop/configs/gameplay/character_desc_zaton.xml`
   **убери две строки**, ранее вручную добавленные для
   `rysal_owl_patch_offer` и `rysal_owl_patch_turn_in`.
   Должны остаться только штатные `actor_dialog`.
5. Перезапусти игру целиком, загрузи сохранение и подойди к Сычу.

## Проверка

1. Сыч предлагает «Есть какие-нибудь заказы на нашивки?».
2. Заказ на бандитов появляется в КПК после принятия.
3. Сдача пяти нашивок отнимает пять штук и даёт 2000 ₽ один раз.
4. После завершения КПК снова можно взять заказ на бандитов.
5. Проверить, что штатные варианты Сыча и старые квесты Бороды
   на месте.

**Не подтверждено запуском на пользовательской сборке.** Ошибки
`FATAL ERROR` или `SCRIPT RUNTIME ERROR` присылать с фрагментом
журнала. При отсутствии диалога и ошибок следующим шагом проверить
фактическое содержимое двух зарегистрированных файлов
`system.ltx` и `character_desc_zaton.xml` после DLTX/XMLOverride.
