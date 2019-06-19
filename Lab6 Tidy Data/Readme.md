# Tidy Data

```python
def extractSexnAge(code):
    pattern = "(\S)(\d+)(\d{2})"
    match = re.search(pattern, code)
    if match:
        sex = match.group(1)
        age = "{}-{}".format(match.group(2), match.group(3))
        return [sex, age]
    else:
        return [np.NaN, np.NaN]
```
## Ejercicio 2 resuelto
```python
df_bill_tidy_ex = (df_bill
                    .melt(
                        id_vars = ["track", "artist.inverted", "year", "time", "genre", "date.entered"],
                        var_name = "week",
                        value_name = "Rank")
                    .assign(week = lambda df: df.week.map(extractWeek))
                    .dropna()
                    .assign(Date = lambda df: computeDate(df["date.entered"], df.week))
                    .rename(columns = {
                        "artist.inverted": "Artist", 
                        "track": "Track", 
                        "year": "Year", 
                        "time": "Time", 
                        "genre": "Genre"})
                    [["Year", "Track", "Artist", "Time", "Genre", "Date", "Rank"]]
                    .sort_values(["Year", "Track"])
               )
```

## Ejercicio 3 resuelto
```python
df_tb_tidy_ex = (df_tb
                .melt(
                    id_vars = ["country", "year"],
                    var_name = "sex_and_age",
                    value_name = "Cases")
                .dropna()
                .assign(sex_and_age = lambda df: df.sex_and_age.map(extractSexnAge))
                .assign(
                    Sex = lambda df: df.sex_and_age.map(lambda v: v[0]),
                    Age = lambda df: df.sex_and_age.map(lambda v: v[1]),
                )
                .rename(columns = {"country": "Country", "year": "Year"})
                [["Country", "Year", "Sex", "Age", "Cases"]]
            )
```