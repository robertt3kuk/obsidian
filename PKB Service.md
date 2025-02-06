Report 
```go 
type Report struct{
	ID uuid.UUID
	Iin string
	Status string 
	ReportID uuid.UUID
	Code []string
	CreatedAt time.Time
}

type SusnSubject struct{
	ID uuid.UUID
	Iin string 
	Status []string
	CreatedAt time.Time	
}

type ArmyStatus struct{
	ID uuid.UUID
	Iin string 
	Status false 
	CreatedAt time.Time
}

```

# Вопросы 
1. Что такое кпд токен 
2. Какую информацию мы сохраняем из Репортов 
3. По каким данным мы определяем что пользователь в сусн справочник по статусу сусн 
4. Проверка по родственным связям с кем 
5. не отправляем ничего кроме иин и реквест айди для сусн 
6. mariageinfo and brithinfo gbdfnl 




# Susn 

1. Если у длина статусов один и там 0 то он не сусн 
2. Если статус коды подходят под список то он является сусн 


# Family 
1.[https://rmrkz.atlassian.net/wiki/spaces/ZS/pages/42729825/LOAN.INT_CLIENTDATA_UPDATE+-](https://rmrkz.atlassian.net/wiki/spaces/ZS/pages/42729825/LOAN.INT_CLIENTDATA_UPDATE+-) 