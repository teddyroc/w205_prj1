# w205_prj1
d
# Leveraging RFM to Identify AGM's Best Customers (2020)

## Recency
나는 recency를 sales 데이터셋의 가장 마지막인 2020-12-31 부터 timedelta-1씩 해가면서 구매 고객을 모니터링 할 것이다. 최근 구매한 고객의 순서대로 리스트에 append하여 참조할 수 있는 리스트를 생성한다.

## Frequency
나는 우선 customers, sales table에서 customer_id당 total_amount를 단일 품목가인 $12로 나누어서 구매한 품목 개수를 구할 것이다. 그 다음, previous sale date와 next sale_date의 timedelta를 보고, 구매한 품목 갯수를 timedelta로 나누어, 고객이 AGM사의 meal을 먹는 빈도수를 구한다. 그리고 1년간의 총 일수에서  이렇게 하는 이유는, 하루 1끼씩 한달 중 총 15일을 AGM사의 건강식으로 먹는데 매일 주문하는 고객과 하루 2끼씩 한달 중 총 30일을 AGM사의 건강식으로 먹지만, 주문을 일주일 치를 미리하여 총 주문 횟수가 적은 사람에 대한 가중치를 반영하기 위해 위와 같이 설정한다.

## Monetary Value
Monetary Value는 1식 $12 균일가이므로, customer_id당 total_amount의 SUM이 가장 큰 고객을 찾는 수식을 모델링합니다. 

## Synthesis
Total amount는 매우 큰 단위를 가지므로, 단순 가중합을 하면 Recency, Frequency 항의 영향력이 매우 낮아질 수 있으므로, 단위를 맞출 수 있는 방법을 고려해 가중합을 한다.
