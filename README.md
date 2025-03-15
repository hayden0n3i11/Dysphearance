import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Simulation Parameters
years = 50  # Number of years to simulate
population = 10_000_000  # Total population
initial_income = 50_000  # Average starting income per year
investment_rate = 0.9  # Percentage of income reinvested each cycle
savings_cap_growth = 0.06  # Savings cap growth per reinvestment cycle
baseline_savings_cap = 5_000  # Initial savings cap
refund_rate = 0.15  # End-of-year refund percentage of investments
inflation_rate = 0.02  # Estimated inflation per year
tax_rate = 0.2  # 20% tax on reinvestment for public services
public_service_fund = 0  # Initial government fund for social programs
max_reinvestment_growth = 0.05  # Limit economic growth per year
digital_currency_adoption_rate = 0.1  # Initial adoption rate, increasing over time

# Initialize tracking arrays
income_levels = np.full(population, initial_income, dtype=float)
savings_caps = np.full(population, baseline_savings_cap, dtype=float)
total_reinvestment = []
total_savings = []
total_refunds = []
total_economy = []
total_tax_collected = []
total_public_service_fund = []
digital_currency_usage = []

# Simulation Loop
for year in range(years):
    # Calculate yearly reinvestments and savings caps
    reinvestment = income_levels * investment_rate
    savings = np.minimum(income_levels * (1 - investment_rate), savings_caps)
    
    # Taxation for public services
    tax_collected = reinvestment * tax_rate
    reinvestment_after_tax = reinvestment - tax_collected
    public_service_fund += tax_collected.sum()  # Accumulate public funds
    
    # Savings cap growth with diminishing returns
    savings_caps += savings_cap_growth * savings_caps * (1 - np.exp(-year / 10))
    
    # End-of-year refunds
    refund = reinvestment_after_tax * refund_rate
    
    # Adjust incomes with diminishing returns on reinvestment growth
    reinvestment_factor = min(reinvestment_after_tax.sum() / (population * initial_income * 10), max_reinvestment_growth)
    income_levels = income_levels * (1 + reinvestment_factor)
    
    # Adjust for inflation
    income_levels *= (1 + inflation_rate)

    # Digital currency adoption rate increase
    digital_currency_adoption_rate += 0.02  # Gradually increasing adoption
    digital_currency_usage.append(digital_currency_adoption_rate * income_levels.sum())

    # Store yearly results
    total_reinvestment.append(reinvestment_after_tax.sum())
    total_savings.append(savings.sum())
    total_refunds.append(refund.sum())
    total_economy.append(income_levels.sum())
    total_tax_collected.append(tax_collected.sum())
    total_public_service_fund.append(public_service_fund)

# Create DataFrame for results
df = pd.DataFrame({
    'Year': np.arange(1, years + 1),
    'Total Reinvestment': total_reinvestment,
    'Total Savings': total_savings,
    'Total Refunds': total_refunds,
    'Total Economy': total_economy,
    'Total Tax Collected': total_tax_collected,
    'Public Service Fund': total_public_service_fund,
    'Digital Currency Usage': digital_currency_usage
})

# Save results to CSV file for further analysis
df.to_csv("Dysphearance_Economic_Model.csv", index=False)

# Display summary of key statistics
print(df.head())

# Plot key economic trends
plt.figure(figsize=(12, 6))
plt.plot(df['Year'], df['Total Reinvestment'], label='Total Reinvestment', linestyle='-', linewidth=2)
plt.plot(df['Year'], df['Total Savings'], label='Total Savings', linestyle='--', linewidth=2)
plt.plot(df['Year'], df['Total Refunds'], label='Total Refunds', linestyle='-.', linewidth=2)
plt.plot(df['Year'], df['Total Economy'], label='Total Economy', linestyle=':', linewidth=2)
plt.plot(df['Year'], df['Total Tax Collected'], label='Total Tax Collected', linestyle='-', linewidth=2)
plt.plot(df['Year'], df['Public Service Fund'], label='Public Service Fund', linestyle='--', linewidth=2)
plt.plot(df['Year'], df['Digital Currency Usage'], label='Digital Currency Usage', linestyle='-.', linewidth=2)

plt.xlabel('Years')
plt.ylabel('Economic Value ($)')
plt.title('Dysphearance Economic Model Simulation Over 50 Years')
plt.legend()
plt.grid(True)
plt.show()
