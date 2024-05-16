## Основные характеристики модели
· Время: t = 0,1,2 , ...
• На рынке идет торговля только одним видом акций с
экзогенно определяемыми случайными дивидендами d_t
• Имеется еще безрисковый актив со ставкой r_f.
• Участники рынка: N трейдеров + «специалист» (аукционист).
· Трейдеры і могут покупать и продавать акции (выбирать x_ti), в
том числе с короткой позицией, беря в долг по ставке rf.
· Трейдеры идентичны, но каждый из них имеет свои ожидания,
которые использует для формирования спроса.
• Трейдеры подают специалисту заявки в виде функций спроса.
· Специалист определяет цену (не агент).
· Акции безгранично делимы. Всего имеется N акций.


## События в период t
· Генерируется очередной дивиденд d_t.
• Трейдеры формируют ожидания величины p_t+1 + d_t+1, а по
ним функции спроса x_ti(p_t).
· Трейдеры сообщают свои функции спроса x_ti(p) аукционисту.
· Аукционист определяет цену на акции в текущем периоде р_t на
основе равновесия спроса и предложения:
Сумма по i от 1 до n x_ti(p_t) = N.
· Трейдеры узнают p_t + d_t и корректируют параметры
прогнозных алгоритмов.

## Подробности
· Процесс для дивидендов - AR(1):
d_t = d(c верхней чертой) +phi(d_t-1-d(с верхней чертой))+epsilon_t.
· В каждый период t у трейдера і момент имеются ожидания по
ПОВОДУ p_t+1 + d_t+1. Используемая информация: цена p_t,
дивиденды dt и вся предыстория цен и дивидендов
(p_t-1, d_t-1, ... ). Используются 2 первых момента:
- условное мат. ожидание (прогноз):
E_ti(p_t) = E_ti[p_t+1 + d_t+1|p_t, d_t, предыстория]
- условная дисперсия (мера риска):
sigma^2_ti(p_t) = var_ti[p_t+1 + d_t+1|p_t, d_t, предыстория]

· Трейдеры формируют свои ожидания путем обучения (см.
далее).
· По 2 первым моментам вычисляется спрос.
Предполагается, что p_t+1 + d_t+1 имеет нормальное
распределение, а функция полезности у трейдера CARA (с
постоянным абсолютным неприятием риска):
u(W)= -exp(-lambda*W).
Тогда из задачи поиска оптимального портфеля спрос на акции
равен

x_ti (p_t) = (E_ti(p_t) -(1+r)p_t) / λσ^2_ti(p_t)

## Уточнение
• Аукционист ограничивает функции спроса сверху и снизу:
x_снизу < x_ti x_cверху.
(Здесь х_снизу < 0, х_Сверху > 0).
• Нет объяснения того, что же должно происходить, если из-за
таких ограничений на спрос равновесной цены не будет
существовать.
· Если же ограничения на спрос не вводить, то не исключено
появление отрицательных равновесных цен.
Существование этих проблем говорит о неполноте описания
модели.

## Построение прогнозов
• А именно, у каждого трейдера есть набор из М предикторов, с
помощью которых вычисляются E_ti(_t), sigma^2_ti. Каждый предиктор ј
дает свой прогноз E_tij(p_t) и измеритель точности sigma^2_ti.
· Прогноз вычисляется по формуле
E_tij(p_t) = a_ij(pt + dt) + bij,
где a_ij, b_ij - параметры предиктора ј агента і.
· В каждый период выбирается один из предикторов трейдера, j*. При этом

E_ti(p_t) = E_tij*(p_t), sigma^2_ti = sigma^2_tij*.

## Работа предикторов
• Предикторы работают по принципу обучающейся системы
классификаторов (learning classifier system).
• Информация об окружающем мире «упаковывается» в строку
битов фиксированной длины JJ.
• Каждый бит – это значение истина или ложь для какого-то
наблюдаемого события, основанного на p_t, d_t и их предыстории.
• Каждый предиктор кроме формулы прогноза и дисперсии
содержит также условие. Может быть выполнено или нет.
• Условие имеет ту же длину, что и строка битов. Состоит из
символов {0, 1, #}. Символ 0 означает, что наблюдаемый бит в
соответствующей позиции должен иметь значение ложь, 1 –
значение истина, а # – произвольное значение.
• В условии одного из предикторов стоят все #.
• Каждый период рассматривается подмножество активных
предикторов агента, то тесть таких, условия которых
выполнены.
• Из активных предикторов агент выбирает предиктор j∗, имеющий наименьшую дисперсию.
• После того, как становится известной новая цена p_t,
показатели точности (дисперсии) всех предикторов, которые
были активными, пересчитываются. Показатель точности
меняется по формуле простого экспоненциального
сглаживания:

σ_t+1,ij = (1 − θ)σ^2_tij + θ[(p_t+1 + d_t+1) − E_ti(p_t)]^2.

## Дескрипторы

Дескрипторы: истина или ложь
Использовались следующие дескрипторы.
С 1 по 6: p_t−1 * r_f / d_t > 0.25, 0.5, 0.75, 0.875, 1.0, 1.125;
с 7 по 10: p_t−1 больше k-периодного скользящего среднего
прошлых цен (MA), где k = 5, 10, 100 и 500;
11. всегда истина (1) ;
12. всегда ложь (0).