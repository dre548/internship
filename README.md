pip install pandas matplotlib
import pandas as pd
import matplotlib.pyplot as plt

file_path = "https://github.com/dre548/internship/blob/main/HIV%20data%202000-2023.csv"

hiv_data = pd.read_csv(file_path, encoding="ISO-8859-1")
hiv_data["Value"] = hiv_data["Value"].str.extract(r"(\d[\d\s]*)")[0].str.replace(" ", "").astype(float) * 1000
hiv_data = hiv_data[hiv_data["Value"].notna()]

global_2023 = hiv_data[hiv_data["Period"] == 2023].groupby("Location")["Value"].sum()
global_total = global_2023.sum()
global_2023 = global_2023 / global_total * 100
global_2023 = global_2023.sort_values(ascending=False)
cumsum = global_2023.cumsum()
top_countries = cumsum[cumsum <= 75].index.tolist()

top_data = hiv_data[hiv_data["Location"].isin(top_countries)]

plt.figure(figsize=(12, 6))
for country in top_countries:
    country_data = top_data[top_data["Location"] == country]
    plt.plot(country_data["Period"], country_data["Value"] / 1e6, label=country)
plt.xlabel("Year")
plt.ylabel("People Living with HIV (Millions)")
plt.title("Trend of HIV Cases in Countries Contributing to 75% of Global Burden (2000-2023)")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
