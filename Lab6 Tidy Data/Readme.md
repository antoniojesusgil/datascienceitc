# Tidy Data

## Ejercicio 2

### Regex
```python
def extractWeek(code):
    pattern = 
    match = re.search(pattern, code)
    if match:
        return int(match.group(1))
    else:
        return np.nan

def computeDate(dates, weeks):
    return pd.to_datetime(dates) + pd.to_timedelta(weeks - 1, unit = 'w')
```

### Completo
```python
df_bill_tidy_ex = (df_bill
                    .melt(
                        id_vars = ["track", "artist.inverted", "year", "time", "genre", "date.entered"],
                        var_name = "week",
                        value_name = "Rank")
                    .assign(week = lambda df: df.week.map(extractWeek))
               )
```