# Инструкция по прошивке принтера Ender 3 S1 (версия Marlin 2.1.1, ЛА включен).

## Внимание!!!
Актуальная прошивка находится в **[Releases](https://github.com/shah-typhoon/Ender-3-S1/releases)**.

## Распаковать файлы
После распаковки в текущей папке должна находиться папка STM32F4_UPDATE с прошивкой вида firmware-<ДАТА>-<ВРЕМЯ>.bin
где ДАТА и ВРЕМЯ это соответственно дата и время компиляции прошивки. Архив с прошивкой экрана находится там же, в Releases по адресу **(https://github.com/shah-typhoon/Ender-3-S1/releases/tag/LCD-files)**

## Первым делом прошиваем экран. 
Данный этап не пригоден для Ender 3 S1 PRO, там другой экран и прошивок под него пока нет. Также нет прошивок для поздних версий экранов S1. 
Для прошивки используется карта памяти MicroSD объемом 8 Гб (если нет нужного объема, можете попробовать карты памяти до 32 Гб), отформатированный FAT32 с размером сектора 4096 байт.
В корень карты памяти копируем папку Private (или DWIN_SET, если Private не прошился). При выключенном принтере отключаем экран, откручиваем заднюю панель. На плате будет разъем для карты памяти. Туда вставляем нашу карту, не устанавливая заднюю панель, подключаем шлейф экрана. Включаем принтер. Начнется процесс прошивки экрана. Если сразу загрузилось меню принтера, значит экран не прошился и надо пробовать другие варианты. Если ничего не помогло, то и так будет работать, но с глюками (артефактами). Позже надо будет поискать прошивку экрана (если есть).
Если прошивка прошла успешно, отключаем принтер, вытаскиваем карту памяти и собираем экран обратно. Больше с экраном ничего делать не придется.

## Приступаем к прошивке самого принтера.
С карты памяти удаляем все файлы, записываем туда папку STM32F4_UPDATE с прошивкой.
#### ВАЖНО. Карту из компьютера надо извлекать правильно, через отключение подключенных устройств.
Вставляем карту в слот принтера, включаем принтер. Если принтер прошивается, появляется лого марлина 2.1.1 и через несколько секунд заружается меню. Идем в Control — Info, видим версию 2.1.1.
Если обновление не прошло, вытаскиваем карту памяти, подключаем в компьютер и добавляем в конец имени файла цифру 1. Например
firmware-20221104-122632_1.bin.
Отключаем карту памяти от компьютера, вставляем в принтер и снова пытаемся прошить. Если не получается, увеличиваем цифру на 1 (1 станет 2,
2 станет 3 и т.д.) и пробуем снова. Если после 3-х попыток не получается, то сообщаем мне. После успешной прошивки настраиваем далее.

## В первую очередь проверяем движение по осям.
Идем в меню Prepare — Homing — Home All. И сразу руку кладем на выключатель на предмет неправильных действий. Можно каждую ось проверить по отдельности (Home X, Home Y, Home Z). Ось Z нужно проверять в последнюю очередь, после X и Y.
При этом голова по оси X должна идти влево до концевика, стол по оси Y назад до концевика, ось Z должна немного приподниматься и идти вниз до срабатывания CrTouch. Если все правильно, продолжаем дальше, иначе надо будет инвертировать движение неправильных осей путем повторной перепрошивки.
По оси Z кончик датчика CrTouch должен находиться ровно над серидиной стола. Если это не так, то после проверки значения X offset (смотрите далее) необходимо настроить оффсеты CrTouch. В прошивке указаны заводские оффсеты.

## Необходимо настроить X offset стола (начало координат)
На некоторых S1 обнаружено разные положения концевика, кончик сопла не всегда находится в нулевом положении. По этой причине в меню принтера включена возможность указать положение концевика. Для этого отправляете ось X домой (Home X) и замеряете расстояние от сопла до стола по горизонтали. Если сопло находится ровно над краем стола, то ничего делать не надо. А если сопло находится левее от края стола, то значение смещения со знаком минус (-) вводите в поле X offset в меню. А если правее, соответственно со знаком плюс (+).

## Настраиваем Z-offset.
Переходим в пункт меню Prepare — Z offset. И прежде всего включаем галочку Live Adjustment. Это позволит в режиме реального времени настраивать Z offset. Первоначальное значение Z offset должно быть 0. Выбираем пункт Home Z axis. Руку держим на выключателе. Кончик сопла должен остановиться на расстоянии 2-3 мм от стола. Если не останавливается и есть риск столкновения сопла со столом, выключаем принтер. Но такая ситуация нереально, если CrTouch работает правильно.
На глаз оцениваем расстояние сопла от стола, и вводим в Z offset значение чуть меньше, чем кажется. Например, если кажется 2 мм, то -1 или -1,5. Значение обязательно должно быть отрицательное. Сопло автоматически опускается на введенное значение. Так последовательно доводим до примерно 0,5 мм. После чего между соплом и столом устанавливаем бумагу и через меню MicroStep Down раз за разом продолжаем опускать сопло и проверяя бумагой. Опускаем до тех пор, пока бумага начнет слегка царапаться об сопло. После чего нажимаем самое нижнее меню Save. Z offset настроено. Но при этом надо понимать, что сопло на высоте около 0,1 мм! Если при печате первый слой ложиться плохо, Z offset можно опустить еще на 0,05 мм. Вплоть до 0,1. Внимание! Зазор 0,1 мм оставляли на случай расширения сопла при нагреве! Если не будет зазора, сопло при печате может царапать стол.

## Обязательно калибруем PIDы нагревателей хотенда и стола.
Температуры калибровки ПИДа необходимо указать те, на которых будете печатать. Если преимущественно PLA, то в районе 200 гр, если PETG, то в районе 230-240 го. Стол соответственно около 50 и 70. Эти температуры не догма, а приведены примерные значения. Их вы сами должны подобрать оптимально для себя.

## На этом прошивка принтера завершена
Далее смотрим цикл видео Соркина и настраиваете шаги мотора экструдера, температуру, потоки, LA и другие параметры принтера. Шаги мотора осей X, Y и Z приведены для заводских деталей и проверки не требуют. Их надо корректировать, если вы будете менять шкивы ремней на соответствующих моторах.
