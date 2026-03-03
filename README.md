# AI Environmental Impact Estimation Methodology

This document explains the full calculation pipeline used to estimate total AI usage and environmental impact across the student population.

---

# 1. Raw Survey Data

The starting dataset contains:

- `Major` (self-reported survey major)
- Weekly prompt counts per model:
  - `ChatGPT Weekly Prompts`
  - `Google Gemini Weekly Prompts`
  - `Claude Weekly Prompts`
- Binary usage indicators per model (0/1):
  - `ChatGPT`
  - `Google Gemini`
  - `Claude (Anthropic)`

Non-users either:
- Have 0 prompts recorded, or
- Have missing prompt values (handled accordingly in estimation).

---

# 2. Population Scaling by Major

Total student population:

```
N_students = 45087
```

Official population proportions by academic field are defined as:

```
major_population_pct = {
    ...
}
```

Each official major population size is computed as:

```
N_major = major_population_pct[major] * N_students
```

Survey majors are mapped to official major categories using:

```
major_mapping = {
    survey_major: official_major
}
```

All unused official categories are grouped into:

```
Other_combined
```

so that total population mass sums to 45,087.

---

# 3. Estimating Total Prompts Per Major × Model

For each:

- Survey major
- Model

We compute:

## 3.1 Weekly Prompt Mean

If prompts are conditional on usage:

```
P_bar = mean(prompts | users)
U_bar = proportion of users
```

Total weekly prompts per student in that major:

```
E[Prompts] = P_bar * U_bar
```

If prompts already include zeros for non-users:

```
Y_bar = mean(prompts)
```

and no usage adjustment is needed.

---

## 3.2 Scale to Total Semester Prompts

Semester length:

```
weeks = 30
```

Scaling constant:

```
scale_constant = N_major * weeks
```

Total prompts estimate:

If using product estimator:

```
T_hat = scale_constant * P_bar * U_bar
```

If using direct mean estimator:

```
T_hat = scale_constant * Y_bar
```

---

# 4. Variance and Confidence Intervals

For product estimator (delta method):

```
Var(T_hat) = (scale_constant^2) * [
    (U_bar^2) * Var(P_bar) +
    (P_bar^2) * Var(U_bar)
]
```

Standard error:

```
SE = sqrt(Var(T_hat))
```

95% Confidence Interval:

```
Lower = T_hat - 1.96 * SE
Upper = T_hat + 1.96 * SE
```

---

# 5. Environmental Conversion

Each model has environmental multipliers:

```
costs[model] = {
    "electric": Wh per prompt,
    "water": ml per prompt,
    "co2": g per prompt
}
```

Environmental totals per major × model:

```
Electric = T_hat * electric
Water = T_hat * water
CO2 = T_hat * co2
```

Confidence intervals scale identically.

---

# 6. Combine Majors (Per Model)

For each model:

Total across majors:

```
T_model = sum(T_major)
```

Variance across majors:

```
Var_model = sum(Var_major)
```

Then compute CI as usual.

---

# 7. Combine Models (Overall AI Impact)

Total AI prompts across all models:

```
T_AI = sum(T_model)
```

Total variance:

```
Var_AI = sum(Var_model)
```

Final 95% CI:

```
Lower_AI = T_AI - 1.96 * sqrt(Var_AI)
Upper_AI = T_AI + 1.96 * sqrt(Var_AI)
```

Environmental totals are aggregated identically.

---

# 8. Final Outputs

The final dataset includes:

- Total AI prompts (30 weeks)
- Electricity usage (Wh)
- Water usage (ml)
- CO2 emissions (g)

Each reported with 95% confidence intervals.

---

# Statistical Assumptions

1. Independence across majors (stratified estimation).
2. Independence across models when aggregating overall impact.
3. Representative survey sampling within each major.
4. Environmental multipliers are constant per prompt.

---

# Summary Pipeline

Raw Survey Data
→ Map to Official Majors
→ Estimate Major × Model Totals
→ Convert to Environmental Impact
→ Aggregate by Model
→ Aggregate Across Models
→ Final Campus-Wide AI Impact CI

---

End of methodology documentation.

