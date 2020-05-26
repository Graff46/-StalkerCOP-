# Схема поведения НПС в оффлайне

Платформа: Зов Припяти.

Использование функционала метода `task` перегруженного в скриптах класса `cse_alife_smart_zone` для навигации НПС применялось ранее Red75

Для использования системы добавте/адаптируйте папку `gamedata` из данного репозитория и вашей игры.
Папка `gamedata_for_test` также добавляется (с переименованием в `gamedata`)/адаптируется в игру для демонстрации и тестов системы.
[Использование демонстрационной версии](https://github.com/Graff46/StalkerCOP_Scripts_Offline_behavior/wiki/%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%B4%D0%B5%D0%BC%D0%BE%D0%BD%D1%81%D1%82%D1%80%D0%B0%D1%86%D0%B8%D0%BE%D0%BD%D0%BD%D0%BE%D0%B9-%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B8)
  
<details>
  <summary>Описание</summary>
 
  Данная система предназначена для управления перемещениеями НПС в оффлайне. Данная схема работает по аналогии с обычными схемами поведения - на системе ini файлов.
</details>

<details>
  <summary>Использование</summary>
  
  
  **Примечания**
  - НПС который берётся под оффлайн логику не должен быть ни в каком смарттеррейне
  - НПС который уже находится под управлением оффлайн логики не может быть взят под смарттеррейн 
  
  Для того чтобы НПС не попадал под смарттеррейны нужно серверному объекту НПС задать поле `no_smart` и задать полю значение `true`, в этом случае НПС не будет забираться в смарттеррейн ни при каких иных условиях. 
  Пример:

```lua
local sobj = alife():create("stalker", vector():set(1,2,3), 1,2)
sobj.no_smart = true
```
_____________

  Использование данной схемы аналогично использованию других схем поведения (на системе ini файлов) в custom_data НПС. Поэтому ниже будут описаны основы составления схемы, учитывая, что Вы знакомы с правилами составления логики для НПС. 
  
  *\* (кондлист) - строка с поддержкой кондлистов `{+-=!} scheme %-+=%`. Про систему кондлистов смотрите уроки создания логики для НПС.*
  
  Для задания схемы поведения в custom_data нужно задать секцию `offline`, а в ней ключ `aсtive` 

> аналогично `aсtive` в `logic`
 
 *Также в сеции `offline` можно указать ключ `cfg` где его значение это путь до файла с логикой относительно папки `config`*
> подобный функционал имеется и при настройке обычной логики

Ключом к `aсtive` должна быть строка (кондлист) с именем секции, (имя секции может быть любым) эта секция будет содержать параметры для поведения НПС (аналогично секциям `remark`, `wolker` и тд. в описании обычной логики НПС).
Секция с параметрами поведения должна **обязательно** иметь 2 ключа: `lv`, `gv` - целевой левел вертекс, гейм вертекс соответственно.

Не обязательные параметры:
1. Переключатели обычные - переключают сразу как только возможно.
2. Переключатели по событиям - срабатывают во время событий;
> Эти переключатели подобны `on_info` в описании обычной логики НПС
#### Переключатели обычные
  Представляют собой ключ `on_over`, где его значение строка (кондлист) c указанием перехода на другую секцию или без.
#### Переключатели по событиям
  Пока событие в системе одно - это достижение НПС целевых вертексов, имя этого события - `end`. Переключатель по событию это ключ (`on_` имя события) (для события `end` - `on_end`). Значение к ключу такое-же как у `on_over`.
  ### Пример настройки логики
```ini
;;;;;; Ниже настрока обычной (онлайн) логики НПС ;;;;;
[logic] 
active = remark

[remark]
anim = hello

;;;;;; Ниже настрока оффлайн логики НПС ;;;;;

[offline] ; Аналогично logic
active = {-infoporsh !func} scheme1 %+infoporsh2%; Устанавливаем активную секцию (любое имя)

[scheme1] ; Секция с параметрами
lv = 998261 ; целевой левел-вертекс
gv = 210 ; целевой гейм-вертекс
on_end = scheme2 %=mytest% ; по событию end переход на секцию scheme2

[scheme2]
lv = 1164741
gv = 184 
on_over = {=offline_pos} scheme1 ; переход на сеекцию scheme1 (сдерживается кондлистом)
```
### Замечания про кондлисты и эффекторы
Как Вы должно быть знаете - функции используемые в кондлистах в качестве прекондишинов (заключаются в `{}`) и в качестве эффекторов (заключаются в `%%`) имеют 2 обязательных аргумента:
1. Клиентский объект актора
2. Клиентский объект НПС

Если если вы используете функции в кондлистах оффлайн логики, то вторым аргументом у функций будет: **Серверный объект НПС!**
Из этого следует, что использование готовых функций из `xr_effects.script` и `xr_conditions.script` **не возможно!** Функции надо писать новые свои с учетов вышеуказанных особенностей!
</details>

[Адаптация к модам и платформам](https://github.com/Graff46/StalkerCOP_Scripts_Offline_behavior/wiki/%D0%90%D0%B4%D0%B0%D0%BF%D1%82%D0%B0%D1%86%D0%B8%D1%8F-%D0%BA-%D0%BC%D0%BE%D0%B4%D0%B0%D0%BC)

Примечание: Схема активируется и парсится при каждом переходе НПС в оффлайн. При переходе НПС в онлайн схема отключается.
