# Introduction
The dataet I used is the Superstore data I gathered from Kaggle that was published by [Laiba Anwer](https://www.kaggle.com/datasets/aditisaxena20/superstore-sales-dataset) over two years ago. It is robust and has over 51,000 rows and 21 columns
# Background
I wanted to investigate and identify what are some of the factors that contirbute to the performance of a superstore, the key metrics to look out for and what areas can the store focus on as it relates to marketing and logistic strategies.

# Tools I used

The tools I used to generate my insights for the project include the following:
- **Python**
- **Visual Studio Code**: My ideal code editor for Python and also SQL.
- **Git and GitHub:** Needed a version control to showcase the steps and changes  made as I progressed with my analysis. It's also ideal for sharing and collaboration purposes.

# The Analysis
## 1.  What is the estimated lifetime value of customers in different segments?

I wanted to figure out which markets were the most value of the long-term and specifically which customer segments in each market was the most valuable for the business using the lifetime value for every customer segment and for every market.

View my notebook showcasing the steps I took:
[CLV_per_Segment](1_CLV_per_segment.ipynb)

## This is the code snippet I used to create my visualization:
```python
l_markets=len(df['market'].unique())

nrows = 4
ncols = 2

fig,ax=plt.subplots(nrows,ncols,figsize=(12,15))
ax = ax.flatten()

df_clv_sum=df_clv.groupby('Market')['CLV'].sum().to_frame(name='Sum').reset_index()
df_clv_merged=df_clv.merge(df_clv_sum,how='inner',on='Market')

market_list=df['market'].unique().tolist()

for i,market in enumerate(market_list):
    df_market=df_clv_merged[df_clv_merged['Market']==market]
    sns.barplot(data=df_market,x='Segment',y='CLV',hue='Segment',ax=ax[i],palette='viridis')
    sns.despine()
    ax[i].set_title(f'Customer Lifetime Value in {market}',fontsize=15,fontweight='bold')
    ax[i].set_ylabel('CLV($)')
    ax[i].set_xlabel('Segment')
    total_clv = df_clv_sum[df_clv_sum['Market'] == market]['Sum'].values[0]
    handles, labels = ax[i].get_legend_handles_labels()#remove message warnings on legends.
    if handles:
        ax[i].legend(title=f'Total CLV=${total_clv:,.2f}', bbox_to_anchor=(1,1), loc='best')

for j in range(len(market_list), len(ax)):# Hide unused subplots.
    ax[j].axis('off')
    
plt.subplots_adjust(hspace=0.7,wspace=0.3)    
fig.tight_layout()

plt.show()
```

### Result
![Visualization of Customer lifetime value for each segment in each market](images/CLV.png)
*Bar graph visualizing the Customer lifetime value for each segment in each market.*
![Visualization of the Total Customer lifetime value for each market](images/CLV_2.png)
*Bar graph visualizing the Customer lifetime value for each segment in each market.*
### Insights
- What stood out is how valuable the APAC market reaching over 5 million dollars in C.L.V showcasing just how valuable the countries in those regions are to the overall bottom line for the company.
- The store also needs to put in more focus in generating more revenue in Canada, EMEA and Africa.

## 2.  Which product categories are driving the most revenue and how do sales figures compare across different product categories?
There aret three main customr segments for the company. I wanted to find out how each customer segment was performing in relation to total sales and whether the growth trend for each market was positive.

View my notebook showcasing the steps I took:
[MarketSales_by_Segments.](2_MarketSalesSegments.ipynb)

## This is the code snippet I used to create my visualization:
```python
fig,ax =plt.subplots(l_markets,1,figsize=(8, 4 *l_markets),sharex=False)

sns.despine()
sns.set_theme(style='ticks')
h_order= ['Home Office','Corporate','Consumer']

for i,market in enumerate(markets):
    df_market = df[df['market']==market]
    df_market_grp =df_market.groupby(['segment','year'])['sales'].sum().to_frame()
    sns.barplot(data=df_market_grp,x='year',y='sales',ax=ax[i],hue='segment',hue_order=h_order,palette='viridis')
    ax[i].set_title(f'Customer Segment sales for {market}',fontsize=15,fontweight='bold')
    ax[i].set_ylabel('Sum of Sales for each Segment($)')
    ax[i].set_xlabel('Years of Corresponding Sales')
    ax[i].legend(title='Segment', bbox_to_anchor=(0, 1.05), loc='upper left')
    plt.subplots_adjust(hspace=0.7)
    
fig.tight_layout()
plt.show()
```
### Result

![Visualization of the Customer segment sales for each market](images/Sales_Segment.png)
*Bar graph visualizing of the Customer segment sales for each market.*

### Insights
- From the graphs, you can notice that there is a psoitive sales growth trend from 2011 to 2014 for all markets and for all the markets, the consumer segment is the main driver of that growth in every year. 

## 3.  How effective are different discount levels in driving sales and Is there a noticeable pattern in sales increase relative to the discount amount?
Considering how frequent the store offers discounts to many of its customers, I wanted to find out which discount levels are driving the most sales and if there is a noticeable pattern or trend

View my notebook showcasing the steps I took:
[3_DiscountEffectiveness](3_DiscountEffectiveness.ipynb)

## This is the code snippet I used to create my visualization:
```python
bins = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
labels = ['0-10', '10-20', '20-30', '30-40', '40-50', '50-60', '60-70', '70-80', '80-90']
df['discount(%)']= df['discount']*100
df['discount_bin'] = pd.cut(df['discount(%)'], bins=bins, labels=labels, right=False)
df=df.drop(labels='discount(%)',axis=1)
df
plt.figure(figsize=(10, 6))
ax=sns.boxplot(y='discount_bin', x='sales', hue='discount_bin',data=df,palette='viridis')
xticks = range(0, int(df['sales'].max()) + 2000, 2000)
ax.set_xticks(xticks)
ax.set_xticklabels([f'{i:,}' for i in xticks])
plt.title('Sales Distribution by Discount Range')
plt.ylabel('Discount_Range')
plt.xlabel('Sales')
plt.show()
```
### Result

![Visualization of the Sales Distribution across different Discount levels](images/Discount_Sales_Distribution.png)
*Boxplot visualizing the Sales Distribution across different Discount levels.*
![Visualization of the different Pearson correlation coefficients for different discount levels](images/Discount_CorrelationX.png)
*Scatterplot visualizing the different Pearson correlation coefficients for different discount levels.*

### Insights
- The boxplot shows just how much the sales vary for different discount levels. The first level i.e. 0 to 10% discount has the most outliers of any of the groups with sales reaching 18,000 dollars for a customer but the further down you the less the outliers are.
- I decided to use a pearson correlation matrix to see if there is a direct linear relationship between different discount levels and sales. For the most part the matrix shows that the relationship either positive or negative is very weak i.e. not even close to reaching 0 or 1. 
- However, it's important to note that the only negative coefficient generated was for the 40 to 50% discount. This is a signal for the store to further investigate the performance of that discount level to figure if ther emight be other factors contributing to the level of sales having a negative relationship or if possible scrap out the discount altogether.

## 4.  What is the Average order value from the top 10 overall buyers and also markets?
I wanted to find out the top 10 highest purchasing customers for the store by indentifying their average order value. I did the same for the markets but I also compared it against the revenue from the product categories in each market.

View my notebook showcasing the steps I took:
[AverageOrderValue](4_AverageOrderValue.ipynb)

## This is the code snippet I used to create my visualization:
```python
sns.barplot(data=customer_AOV,x='customer_name',y='AOV_per_Customer',hue='total_revenue',palette='inferno')
sns.despine()
plt.title('Most Valuable Customers in relation to A.O.V',fontweight='bold')
plt.xlabel('Customers')
plt.ylabel('A.O.V per customer')
plt.legend(loc='best',title='Total Revenue',bbox_to_anchor=(1.05,1))
plt.xticks(rotation=45,ha='right')
plt.show()

```
```python
sns.barplot(data=market_AOV,x='market',y='AOV_per_market',ax=ax[0],hue='total_revenue',palette='inferno')
sns.despine()
ax[0].set_title('Most Valuable Markets in relation to A.O.V',fontweight='bold')
ax[0].set_xlabel('Markets')
ax[0].set_ylabel('A.O.V per market')
ax[0].legend(loc='best',title='Total Revenue',bbox_to_anchor=(1.05,1))
ax[0].set_xticklabels(ax[0].get_xticklabels(),rotation=45,ha='right')

sns.barplot(data=df_category,x='market_',y='sum_sales',ax=ax[1],hue='category_',palette='viridis')
sns.despine()
ax[1].set_title('Revenue per Product Category in each Market',fontweight='bold')
ax[1].set_xlabel('Markets')
ax[1].set_ylabel('Total Revenue per Category')
ax[1].legend(loc='best',title='Product Category',bbox_to_anchor=(1.05,1))
ax[1].set_xticklabels(ax[1].get_xticklabels(),rotation=45,ha='right')

fig.tight_layout()
plt.show()

```
### Result

![Visualization of the Average order value of the top 10 store buyers](images/AOV_customer.png)
*Bar graph visualizing of the Average order value of the top 10 store buyers.*
![Visualization of the Avergae order value for each market in comparison to their revenues per product category](images/AOV_market.png)
*Bar graph visualizing the comparison of the Average order value of the markets against the revenue of different product categories.*

### Insights
- The first graph showcases the customers the store should target more using marketing strategies such as specialized promotions, increased discount offers on bulk orders or any specialized deals.
- The second showcases just how valuable the APAC market is for the store together with the EU. Furthermore, when comparing agains the revenue generated per product category, it shows just how valuable technology products are. The store could start generating digital marketing strategies to maximize on customers purchasing technology products. Tactics like specialized promotions that fit such markets could prove pivotal in increasing sales in the near future.

## 5.  
### 5.1 How does shipping cost and shipping mode affect Sales ?
A big part of the store's business involves shipping goods worldwide so I wanted to figure out how the shipping costs and shipping modes they used affected their sales across the world.

View my notebook showcasing the steps I took:
[Shipping&GrowthRate](5_Shipping&GrowthRate.ipynb)

## This is the code snippet I used to create my visualization:
```python
fig, ax=plt.subplots(1,2,figsize=(10,5))

df_ship_perc=df.pivot_table(values=['sales','shipping_cost'],index=['ship_mode','year'],aggfunc='sum')
df_ship_perc['shipping_cost_percentage']=(df_ship_perc['shipping_cost']/df_ship_perc['sales'])*100
df_ship_perc
sns.barplot(data=df_ship_perc,x='ship_mode',y='shipping_cost_percentage',ax=ax[0],hue='year',palette='viridis')
sns.despine()
ax[0].set_ylabel('Shipping Cost as(%)')
ax[0].set_xlabel('Shipping Mode')
ax[0].legend(loc='best',title='Year',bbox_to_anchor=(1,1))
ax[0].set_title('Shipping cost distribution per Shipping Mode',fontweight='bold')
ax[0].set_xticklabels(ax[0].get_xticklabels(),rotation=45,ha='right')
ax[0].set_ylim(0,20)

df_piv = df.pivot_table(values=['sales','shipping_cost'],index=['ship_mode','year'],aggfunc='sum').sort_values(by='ship_mode')
sns.barplot(data=df_piv,x='ship_mode',y='sales',hue='year',ax=ax[1],palette='viridis')
sns.despine()
ax[1].set_ylabel('Total Sales per Shipping Mode')
ax[1].set_xlabel('Shipping Mode')
ax[1].legend(loc='best',title='Year',bbox_to_anchor=(1,1))
ax[1].set_title('Total Sales distribution per Shipping Mode',fontweight='bold')
ax[1].set_xticklabels(ax[0].get_xticklabels(),rotation=45,ha='right')

plt.tight_layout()
plt.show()
```
### Result
![Visualization of the Shipping mode and costs and how they relate to overall sales](images/ShippingMode&Costs.png)
*Two-sided Bar graph visualizing the Shipping mode and costs and how they relate to overall sales.*

### Insights
- The shipping costs for all four shipping modes stary relatively the same from 2011 to 2014 with the highest costs belonging to the first class and the lowest to the standard class.
- The biggest takeaway, however, is that the highest overall sales are linked to the standard class compared to the rest, indicating that many of the store's customers prefer to wait and use the more pocket-friendly shipping mode.

### 5.2.  Which markets show promise in relation of order growth rate ? 
I also wanted to identify the rate of growth of orders by filtering out the data on a year-by-year basis and identifying which markets showed the highest overall growth of total orders.

View my notebook showcasing the steps I took:
[Shipping&GrowthRate](5_Shipping&GrowthRate.ipynb)

## This is the code snippet I used to create my visualization:
```python
fig, ax=plt.subplots(1,2,figsize=(13,5))

sns.barplot(data=df_piv_mean,x='market',y='mean_count_growth_rate',ax=ax[0],hue='mean_sales',palette='viridis')
sns.despine()
ax[0].set_ylabel('Mean order count growth rate (%)')
ax[0].set_xlabel('Market')
ax[0].legend(loc='best',title='Avg Sale per order',bbox_to_anchor=(0.85,1))
ax[0].set_title('Highest Growth rate by order count per Market',fontweight='bold')
ax[0].set_xticklabels(ax[0].get_xticklabels(),rotation=45,ha='right')

sns.barplot(data=df_piv,x='market',y='count_growth_rate',ax=ax[1],hue='year',palette='viridis')
sns.despine()
ax[1].set_ylabel('order growth rate per year(%)')
ax[1].set_xlabel('Market')
ax[1].legend(loc='best',title='Year',bbox_to_anchor=(1,1))
ax[1].set_title('Growth rate by order count per Market(Year-by-Year)',fontweight='bold')
ax[1].set_xticklabels(ax[0].get_xticklabels(),rotation=45,ha='right')

plt.tight_layout()
plt.show
```
### Result

![Visualization of the overall growth rate of orders by market](images/marketgrowthrate.png)
*Two-sided Bar graph visualizing the growth rate of orders by market.*

### Insights
- What stands out when looking at the two graphs is that the market, EMEA , shows the most promise of any of the markets. It had a a steady growth rate of orders and the highest overall growth of orders followed closely by the EU. This data could prove crucial for the store to help center its marketing initiatives more on EMEA to maximize order growth and generate even more revenue.

## 6.  Which markets generated the most profit per unit and what's the correlation between net profit and major performance metrics ? 

View my notebook showcasing the steps I took:
[NetProfit](6_NetProfit.ipynb)

## This is the code snippet I used to create my visualization:

```python
fig, axs = plt.subplots(1, 3, figsize=(16, 7), sharex=True, sharey=True)

sns.heatmap(pearson_df, annot=True, cmap='coolwarm', vmin=-1, vmax=1, ax=axs[0])
axs[0].set_title('Pearson Correlation',fontweight='bold')

sns.heatmap(spearman_df, annot=True, cmap='coolwarm', vmin=-1, vmax=1, ax=axs[1])
axs[1].set_title('Spearman Correlation',fontweight='bold')

sns.heatmap(kendall_df, annot=True, cmap='coolwarm', vmin=-1, vmax=1, ax=axs[2])
axs[2].set_title('Kendall Correlation',fontweight='bold')

plt.tight_layout()
plt.suptitle('Three Major Correlational Matrices for the 5 major numerical metrics',fontsize=15,fontweight='bold')
plt.subplots_adjust(top=0.9) 
plt.show()
```

```python
df_netp=df.pivot_table(values=['sales','profit','quantity'],index=['market','category'],aggfunc='sum').reset_index()
df_netp['net_profit_perc']=(df_netp['profit']/df_netp['sales'])*100
df_netp['profit_per_unit']=(df_netp['profit']/df_netp['quantity'])*100
df_netp
sns.barplot(data=df_netp,x='market',y='profit',hue='category',palette='inferno')
sns.despine()
plt.title('Profit per Unit for each product category',fontweight='bold')
plt.xlabel('Market')
plt.ylabel('Profit per Unit')
plt.legend(loc='best',title='Product category',bbox_to_anchor=(1.05,1))
plt.xticks(rotation=45,ha='right')
plt.show()
```
### Result
![Visualization of the profit per unit for all markets](images/profit_per_unit.png)
*Bar graph visualizing the profit per unit for all markets.*


![Visualization of the correlation between net profit and other major performance metrics](images/NetProfit_Correlation.png)
*Three-sided Heatmap visualizing the correlation between net profit and other major performance metrics.*

### Insights
- The APAC market was the best performing marketing in relation to profit per unit and the best product category in the market was technology reaching over 200,000 dollars in profit per unit.
- Using the following metrics i.e. net  percentage,sales, profit, quantity and profit per unit, I decided to see if there is any correlation between them and fr further accuracy I used all three correlational coefficient matrices to figure out if there were any relationships that stood out. I identified two i.e. Quantity against sales and Quantity against profit per unit.
- The first one had a positive correlation that was between 0.533975 to 0.757143 signalling a positive linear correlation meaning as quantity increases sales for the store also increases which makes sense.
- The second one was intriguing. It showed a negative correlation i.e. from -0.323810 to -0.496104 showing an inverse association between the variables. Some possible reasons why this could be might include : increased purchases of bulk orders resulting in increaased shipping costs affecting the overall profit per unit, high bulk purchases of low profit margin products essentially contributing to high total sales but low profits overall, or higher quantities purchased might result in higher discounts offered affecting the overall bottom line of the store.

# Conclusions
This dataset was very interesting to evaluate. It showed an overall view of how super stores perform and the main takeaways I gathered from the data include:

- The store needs to start prioritizing the APAC, EMEA and EU markets as all three markets show promise for potential growth in bth orders and sales.
- The store needs to invest in a more sustainable strategy to maximise on the future customers who prefer to ship their products using the Standard class as a large proportion of sales are attributed to that specific shipping mode.

# What I learned
I was able to improve upon my python skills, analyze data and gain curcial insights into how superstore operates and the main metrics to look out for.