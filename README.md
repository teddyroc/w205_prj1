# w205_prj1

# Leveraging RFM to Identify AGM's Best Customers (2020)

## Recency
나는 recency를 sales 데이터셋의 가장 마지막인 2020-12-31 부터 일 혹은 주 단위로 window size를 설정하여 일정 timedelta씩 이동해가면서 구매 고객을 모니터링 할 것이다. 간단하게 Window가 이동한 횟수의 역수를 Recency 지수로 사용한다. 예를 들면, 1주 단위로 window를 설정했으면, 12/25~12/31에 있으면 1, 12/19~12/25 사이에 있으면 0.5 등으로 설정한다. 따라서 Recency 지수는 클수록 Best Customer이다.
In this analysis, Recency measures how recently a customer has made a purchase, relative to the end of our dataset on December 31, 2020. I will set a fixed window size—either daily or weekly—and move this window backwards from the final date in the data at regular time intervals (timedelta). The Recency score is defined as the product of the reciprocals of the window indices. For example, with a weekly window, a customer whose last purchase falls between December 25 and December 31 will have a Recency score of 1/(1). If the customer purchased between December 19 and December 25, they will have a score of 1/(1) * 1/(1+1), and so on. Therefore, A higher Recency score indicates a more recent purchase and consequently reflects a stronger customer relationship.

## Frequency
나는 단위 기간 AGM사의 meal 섭취 빈도수를 Frequency 지수로 사용할 것이다. 우선 customers, sales table에서 customer_id당 total_amount를 단일 품목가인 $12로 나누어서 구매한 품목 개수를 구할 것이다. 그 다음, previous sale date와 next sale_date의 timedelta를 보고, 구매한 품목 갯수를 timedelta로 나누어, 고객이 단위 기간동안 AGM사의 meal을 먹는 빈도수를 구한다. 또는 1년간의 총 일수에서 구매한 품목 개수를 나누어 1년 안에서의 단위 기간동안 AGM사의 meal을 섭취하는 빈도수를 구한다. 시각화를 통해 좀 더 타당한 time window를 선정하고, 지수를 산출한다. 이렇게 하는 이유는, 하루 1끼씩 1달 중 총 15일을 AGM사의 건강식으로 먹는데 매일 주문하는 고객과 하루 2끼씩 한달 중 총 30일을 AGM사의 건강식으로 먹지만, 주문을 일주일 치를 미리하여 총 주문 횟수가 적은 사람에 대한 가중치를 반영하기 위해서이다. 따라서 Frequency 지수는 클수록 Best Customer이다.

I will use the frequency of AGM’s meal consumption over a given time period as the Frequency index. First, I will calculate the time differences between consecutive purchases for each customer. Then, I will divide the number of purchased items - calculated as the total_amount for the given period divided by $12 - by the corresponding time intervals to obtain the consumption frequency of AGM meals within the chosen time unit. Or, I can divide the total number of purchased items by the total number of days in a year to estimate the average frequency of AGM meal consumption per day over a year.
Based on visualizations, I will select the most appropriate time window and compute the index. The reason for this approach is to account for differences in ordering patterns—for example, a customer who eats one AGM healthy meal per day for 15 days in a month and orders daily, versus a customer who eats two AGM healthy meals per day for 30 days in a month but places a single order once a week, resulting in fewer total orders. By capturing the actual consumption pattern rather than just the raw order count, the Frequency index provides more accurate weighting. Therefore, a higher Frequency score indicates a best customer. 

** consecutive purchases : 연속적인, 순차적인 구매


## Monetary Value
Monetary Value는 1식 $12 균일가이므로, customer_id당 total_amount의 SUM이 가장 큰 고객을 찾는 수식을 모델링합니다. 이 경우, 따로 time window를 설정하여 분석하기보다는 1년에 걸친 총 구매량이 Monetary Value를 대변한다고 생각하기때문에, Sum 값을 구해서 Monetary Value 지수로 사용합니다. 따라서 Monetary Value가 클 수록 Best Customer이다.
I will use the annual sum of total_amount for each customer_id as the Monetary Value index. Rather than defining a separate time window for the analysis, I regard the total purchase amount over the course of a year as representative of the Monetary Value. Therefore, customers with the highest Monetary Value are considered the best customers. 

## Synthesis
Recency, Frequency, Monetary Value 모두 클수록 좋지만, Total amount는 두 가지 지수와 다르게 매우 큰 단위를 가지므로, 단순 가중합을 하면 Recency, Frequency 항의 영향력이 매우 낮아질 수 있다. 각 지수 안에서 min-max scaling을 통해 단위를 맞춰주고, 각 지수의 가중합을 w_r*Recency + w_f*Frequency + w_mv*Monetary_value 로 하여 RFM 지수를 산출한다.
While higher values of Recency, Frequency, and Monetary Value are all desirable, the total amount column (Monetary Value) has a much larger scale compared to the other two metrics. Therefore, if we simply take a weighted sum, the influence of the Recency and Frequency terms could be significantly diminished. To address this, we apply min–max scaling within each metric to align their units, and then calculate the RFM score as a weighted sum:
RFM score = w_r × Recency + w_f × Frequency + w_mv × Monetary_Value.
