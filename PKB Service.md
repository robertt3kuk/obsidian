Report 
```go 
type Report struct{
	ID uuid.UUID
	Iin string
	Status string 
	ReportID uuid.UUID
	FoundInSources int
	CreatedAt time.Time
}

type SusnSubject struct{
	ID uuid.UUID
	Iin string 
	Susn bool 
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




