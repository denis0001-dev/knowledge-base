#library #kotlin #code

A multiplatform Kotlin library for working with date and time.

```kotlin
implementation("org.jetbrains.kotlinx:kotlinx-datetime:0.6.2")
```
## Operations
### Getting the current moment of time

The current moment of time can be captured with the `Instant` type. 
To obtain an `Instant` corresponding to the current moment of time, 
use its default implementation `Clock.System` that represents the system clock:

```kotlin
val currentMoment = Clock.System.now()
```

### Converting an instant to local date and time components

An `Instant` is just a counter of high resolution time intervals since the beginning of time scale.
To get human readable components from an `Instant` value, you need to convert it to the `LocalDateTime`
type that represents date and time components without a reference to the particular time zone.

The `TimeZone` type provides the rules to convert instants from and to datetime components.

```kotlin
val currentMoment: Instant = Clock.System.now()
val datetimeInUtc: LocalDateTime = currentMoment.toLocalDateTime(TimeZone.UTC)
val datetimeInSystemZone: LocalDateTime = currentMoment.toLocalDateTime(TimeZone.currentSystemDefault())
```

A `LocalDateTime` instance exposes familiar components of the Gregorian calendar: 
`year`, `month`, `day`, `hour`, and so on up to `nanosecond`.
The property `dayOfWeek` shows what weekday that date is,
and `dayOfYear` shows the day number since the beginning of a year.


Additional time zones can be acquired by their string identifier with the `TimeZone.of(id: String)` function.
```kotlin
val tzBerlin = TimeZone.of("Europe/Berlin")
val datetimeInBerlin = currentMoment.toLocalDateTime(tzBerlin)
```

A `LocalDateTime` instance can be constructed from individual components:

```kotlin
val kotlinReleaseDateTime = LocalDateTime(2016, 2, 15, 16, 57, 0, 0)
```

An instant can be obtained from `LocalDateTime` by interpreting it as a time moment
in a particular `TimeZone`:

```kotlin
val kotlinReleaseInstant = kotlinReleaseDateTime.toInstant(TimeZone.of("UTC+3"))
```

### Getting local date components

A `LocalDate` represents a local date without time. You can obtain one from an `Instant`
by converting it to `LocalDateTime` and taking its `date` property.

```kotlin
val now: Instant = Clock.System.now()
val today: LocalDate = now.toLocalDateTime(TimeZone.currentSystemDefault()).date
// or shorter
val today: LocalDate = Clock.System.todayIn(TimeZone.currentSystemDefault())
```
Note, that today's date really depends on the time zone in which you're observing the current moment.

`LocalDate` can be constructed from three components, year, month, and day:
```kotlin
val knownDate = LocalDate(2020, 2, 21)
```

### Getting local time components

A `LocalTime` represents local time without date. You can obtain one from an `Instant`
by converting it to `LocalDateTime` and taking its `time` property.

```kotlin
val now: Instant = Clock.System.now()
val thisTime: LocalTime = now.toLocalDateTime(TimeZone.currentSystemDefault()).time
```

A `LocalTime` can be constructed from four components, hour, minute, second and nanosecond:
```kotlin
val knownTime = LocalTime(hour = 23, minute = 59, second = 12)
val timeWithNanos = LocalTime(hour = 23, minute = 59, second = 12, nanosecond = 999)
val hourMinute = LocalTime(hour = 12, minute = 13)
```

### Converting instant to and from unix time

An `Instant` can be converted to a number of milliseconds since the Unix/POSIX epoch with the `toEpochMilliseconds()` function.
To convert back, use the companion object function `Instant.fromEpochMilliseconds(Long)`.

### Converting instant and local datetime to and from the ISO 8601 string

`Instant`, `LocalDateTime`, `LocalDate` and `LocalTime` provide shortcuts for
parsing and formatting them using the extended ISO 8601 format.
The `toString()` function is used to convert the value to a string in that format, and 
the `parse` function in companion object is used to parse a string representation back. 

```kotlin
val instantNow = Clock.System.now()
instantNow.toString()  // returns something like 2015-12-31T12:30:00Z
val instantBefore = Instant.parse("2010-06-01T22:19:44.475Z")
```

`LocalDateTime` uses a similar format, but without `Z` UTC time zone designator in the end.

`LocalDate` uses a format with just year, month, and date components, e.g. `2010-06-01`.

`LocalTime` uses a format with just hour, minute, second and (if non-zero) nanosecond components, e.g. `12:01:03`.

```kotlin
LocalDateTime.parse("2010-06-01T22:19:44")
LocalDate.parse("2010-06-01")
LocalTime.parse("12:01:03")
LocalTime.parse("12:00:03.999")
LocalTime.parse("12:0:03.999") // fails with an IllegalArgumentException
```

### Working with other string formats

When some data needs to be formatted in some format other than ISO 8601, one
can define their own format or use some of the predefined ones:

```kotlin
// import kotlinx.datetime.format.*

val dateFormat = LocalDate.Format {
    monthNumber(padding = Padding.SPACE)
    char('/')
    day()
    char(' ')
    year()
}

val date = dateFormat.parse("12/24 2023")
println(date.format(LocalDate.Formats.ISO_BASIC)) // "20231224"
```

#### Using Unicode format strings (like `yyyy-MM-dd`)

Given a constant format string like the ones used by Java's
[DateTimeFormatter.ofPattern](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html) can be
converted to Kotlin code using the following invocation:

```kotlin
// import kotlinx.datetime.format.*

println(DateTimeFormat.formatAsKotlinBuilderDsl(DateTimeComponents.Format {
    byUnicodePattern("uuuu-MM-dd'T'HH:mm:ss[.SSS]Z")
}))

// will print:
/*
date(LocalDate.Formats.ISO)
char('T')
hour()
char(':')
minute()
char(':')
second()
alternativeParsing({
}) {
    char('.')
    secondFraction(3)
}
offset(UtcOffset.Formats.FOUR_DIGITS)
 */
```

When your format string is not constant, with the `FormatStringsInDatetimeFormats` opt-in,
you can use the format without converting it to Kotlin code beforehand:

```kotlin
val formatPattern = "yyyy-MM-dd'T'HH:mm:ss[.SSS]"

@OptIn(FormatStringsInDatetimeFormats::class)
val dateTimeFormat = LocalDateTime.Format {
    byUnicodePattern(formatPattern)
}

dateTimeFormat.parse("2023-12-24T23:59:59")
```

### Parsing and formatting partial, compound or out-of-bounds data

Sometimes, the required string format doesn't fully correspond to any of the
classes `kotlinx-datetime` provides. In these cases, `DateTimeComponents`, a
collection of all datetime fields, can be used instead.

```kotlin
// import kotlinx.datetime.format.*

val yearMonth = DateTimeComponents.Format { year(); char('-'); monthNumber() }
    .parse("2024-01")
println(yearMonth.year)
println(yearMonth.monthNumber)

val dateTimeOffset = DateTimeComponents.Formats.ISO_DATE_TIME_OFFSET
    .parse("2023-01-07T23:16:15.53+02:00")
println(dateTimeOffset.toUtcOffset()) // +02:00
println(dateTimeOffset.toLocalDateTime()) // 2023-01-07T23:16:15.53
```

Occasionally, one can encounter strings where the values are slightly off:
for example, `23:59:60`, where `60` is an invalid value for the second.
`DateTimeComponents` allows parsing such values as well and then mutating them
before conversion.

```kotlin
val time = DateTimeComponents.Format { time(LocalTime.Formats.ISO) }
    .parse("23:59:60").apply {
        if (second == 60) second = 59
    }.toLocalTime()
println(time) // 23:59:59
```

Because `DateTimeComponents` is provided specifically for parsing and
formatting, there is no way to construct it normally. If one needs to format
partial, complex or out-of-bounds data, the `format` function allows building
`DateTimeComponents` specifically for formatting it:

```kotlin
DateTimeComponents.Formats.RFC_1123.format {
    // the receiver of this lambda is DateTimeComponents
    setDate(LocalDate(2023, 1, 7))
    hour = 23
    minute = 59
    second = 60
    setOffset(UtcOffset(hours = 2))
} // Sat, 7 Jan 2023 23:59:60 +0200
```

### Instant arithmetic

```kotlin
val now = Clock.System.now()
val instantInThePast: Instant = Instant.parse("2020-01-01T00:00:00Z")
val durationSinceThen: Duration = now - instantInThePast
val equidistantInstantInTheFuture: Instant = now + durationSinceThen
```

`Duration` is a type from the experimental `kotlin.time` package in the Kotlin standard library.
This type holds the amount of time that can be represented in different time units: from nanoseconds to 24H days.

To get the calendar difference between two instants you can use the `Instant.periodUntil(Instant, TimeZone)` function.

```kotlin
val period: DateTimePeriod = instantInThePast.periodUntil(Clock.System.now(), TimeZone.UTC)
```

A `DateTimePeriod` represents a difference between two particular moments as a sum of calendar components, 
like "2 years, 3 months, 10 days, and 22 hours".

The difference can be calculated as an integer amount of specified date or time units:

```kotlin
val diffInMonths = instantInThePast.until(Clock.System.now(), DateTimeUnit.MONTH, TimeZone.UTC)
```
There are also shortcuts `yearsUntil(...)`, `monthsUntil(...)`, and `daysUntil(...)`.

A particular amount of datetime units or a datetime period can be added to an `Instant` with the `plus` function:

```kotlin
val now = Clock.System.now()
val systemTZ = TimeZone.currentSystemDefault()
val tomorrow = now.plus(2, DateTimeUnit.DAY, systemTZ)
val threeYearsAndAMonthLater = now.plus(DateTimePeriod(years = 3, months = 1), systemTZ)
```

Note that `plus` and `...until` operations require a `TimeZone` as a parameter because the calendar interval between 
two particular instants can be different, when calculated in different time zones.

### Date arithmetic

Similar operations with date units are provided for `LocalDate` type:

- `LocalDate.plus(number, DateTimeUnit.DateBased)`
- `LocalDate.plus(DatePeriod)`
- `LocalDate.until(LocalDate, DateTimeUnit.DateBased)` and the shortcuts `yearsUntil`, `monthUntil`, `daysUntil`
- `LocalDate.periodUntil(LocalDate): DatePeriod` and `LocalDate.minus(LocalDate): DatePeriod`

Notice that, instead of the general `DateTimeUnit` and `DateTimePeriod` types, we're using their subtypes
`DateTimeUnit.DateBased` and `DatePeriod` respectively. This allows preventing the situations when
time components are being added to a date at compile time.

### Date + time arithmetic

Arithmetic on `LocalDateTime` is intentionally omitted. The reason for this is that the presence of daylight saving time
transitions (changing from standard time to daylight saving time and back) causes `LocalDateTime` arithmetic to be
ill-defined. For example, consider time gaps (or, as [`dst` tag wiki on Stack Overflow](https://stackoverflow.com/tags/dst/info)
calls them, "spring forward" transitions), that is, ranges of date + time combinations that never occur in a given
time zone due to clocks moving forward. If we allowed `LocalDateTime` arithmetic that ignored time zones, then it
could result in `LocalDateTime` instances that are inside a time gap and are invalid in the implied time zone.

Therefore, the recommended way to use a `LocalDateTime` is to treat it as a representation of an `Instant`,
perform all the required arithmetic on `Instant` values, and only convert to `LocalDateTime` when a human-readable
representation is needed.

```kotlin
val timeZone = TimeZone.of("Europe/Berlin")
val localDateTime = LocalDateTime.parse("2021-03-27T02:16:20")
val instant = localDateTime.toInstant(timeZone)

val instantOneDayLater = instant.plus(1, DateTimeUnit.DAY, timeZone)
val localDateTimeOneDayLater = instantOneDayLater.toLocalDateTime(timeZone)
// 2021-03-28T03:16:20, as 02:16:20 that day is in a time gap

val instantTwoDaysLater = instant.plus(2, DateTimeUnit.DAY, timeZone)
val localDateTimeTwoDaysLater = instantTwoDaysLater.toLocalDateTime(timeZone)
// 2021-03-29T02:16:20
```