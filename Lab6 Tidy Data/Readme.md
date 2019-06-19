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