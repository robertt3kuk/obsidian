Report 
```go 
type Report strutct{
	ID uuid.UUID
	Iin string
	Status string 
	ReportID uuid.UUID
	FoundInSources int
	CreatedAt time.Time
}


```

# Вопросы 
1. Что такое кпд токен 
2. Какую информацию мы сохраняем из Репортов 
3. По каким данным мы определяем что пользователь в сусн справочник по статусу сусн 
4. Проверка по родственным связям с кем 