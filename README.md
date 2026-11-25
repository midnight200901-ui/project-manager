# Личный кабинет успеваемости студента

Командный учебный проект (36 ч, 6 спринтов): мобильное приложение для просмотра оценок по дисциплинам, расчёта среднего балла, отслеживания динамики и расчёта «какая оценка нужна для целевого среднего».

**Стек:** Kotlin · Jetpack Compose (Material 3) · Navigation Compose · Room (SQLite) + KSP · ViewModel + StateFlow · JUnit 4 .  

## Как открыть и запустить

1. Android Studio Koala (2024.1) или новее, JDK 17 (встроенный JDK Android Studio подходит).
2. `File → Open…` → выбрать папку проекта. Дождаться Gradle Sync (нужен интернет, Gradle 8.7 скачается по `gradle-wrapper.properties`).
3. Выбрать эмулятор или устройство (Android 8.0+, API 26+) → **Run ▶**.
4. Первый запуск: база пуста. На главном экране нажмите **«Загрузить тестовые данные»** (или меню ⋮).

Сборка APK: `Build → Build Bundle(s) / APK(s) → Build APK(s)`.
Юнит-тесты расчётов: `./gradlew test` (или ПКМ на `app/src/test` → Run Tests).

> Если Android Studio не нашла `gradlew`: Settings → Build Tools → Gradle → Gradle JDK = 17; затем при необходимости выполнить `gradle wrapper --gradle-version 8.7` в корне проекта.

## Структура проекта

```
app/src/main/java/com/example/gradetracker/
├─ MainActivity.kt
├─ data/        Entities.kt · Daos.kt · AppDatabase.kt · GradeRepository.kt · SampleData.kt   (Спринт 2)
├─ domain/      GradeRules · GradeCalculator · GradeFilter · Validator · Formatters           (Спринты 1, 4, 5)
└─ ui/          GradeApp (навигация) · GradesViewModel · HomeScreen · DisciplineScreen ·
                FormScreens · DynamicsScreen · CalculatorScreen · Components · Theme         (Спринты 3–5)
app/src/test/   GradeCalculatorTest · ValidatorAndFilterTest                                 (Спринт 5)
docs/           материалы по каждому спринту, тест-кейсы, инструкция, отчёт по ролям
```

## Что реализовано

| Требование | Где |
|---|---|
| Главный экран: общий средний, число оценок, сводка по дисциплинам | `HomeScreen` |
| Список дисциплин (название, преподаватель, средний балл) + подсветка низкого среднего | `HomeScreen`, `GradeRules.LOW_AVERAGE_THRESHOLD = 3,5` |
| Экран дисциплины: дата, вид работы, оценка, комментарий | `DisciplineScreen` |
| Добавление / редактирование / удаление оценки и дисциплины, валидация 2–5 | `FormScreens`, `Validator` |
| Средний по дисциплине и общий (без деления на ноль) | `GradeCalculator.average` |
| Фильтры (вид работы, период-месяц) и сортировка (дата / оценка) | `GradeFilter`, `DisciplineScreen` |
| Динамика по месяцам: график + таблица | `DynamicsScreen`, `GradeCalculator.monthlyDynamics` |
| Калькулятор целевого балла (в т.ч. «недостижимо») | `CalculatorScreen`, `GradeCalculator.requiredNextGrade` |
| Пустые состояния, подтверждение удаления, каскадное удаление с предупреждением | все экраны |

Не реализовано (по заданию это «усложнённая версия»): взвешенный средний, сценарный прогноз 3/4/5, пользовательский порог риска. Архитектура позволяет добавить их без переделки (вес — поле/словарь по `workType` + новая функция в `GradeCalculator`).

## Честно о проверке

Код написан без доступа к Android SDK, поэтому **в среде разработки этого проекта он не компилировался и не запускался**. Формулы расчётов и контрольные числа в `docs/` проверены независимой Python-репликой алгоритма. Первый шаг команды — Sync + Run + `./gradlew test` и заполнение `docs/test-cases.md` по факту (колонка «Результат»).
