---
title: Инструкции по GORM
layout: страница
---

Фантастическая библиотека ORM для Golang призвана быть дружественной для разработчиков.

## Обзор

* Полнофункциональный ORM
* Связи (Has One (имеет одну), Has Many (имеет много), Belongs To (принадлежит), Many To Many (многие ко многим), Polymorphism (полиморфизм), Single-table inheritance (одно табличное представление))
* Хуки (До/После Создать/Сохранить/Обновить/Удалить/Найти)
* Нетерпеливая загрузка с помощью `Preload`, `Joins`
* Транзакции, вложенные транзакции, точки сохранения, откат к сохраненной точке
* Контекст, Режим подготовки, Режим DryRun
* Batch Insert, FindInBatches, Find/Create with Map, CRUD with SQL Expr and Context Valuer
* SQL Builder, Upsert, Locking, Optimizer/Index/Comment Hints, Named Argument, SubQuery
* Composite Primary Key, Indexes, Constraints
* Автомиграция
* Logger
* Extendable, flexible plugin API: Database Resolver (Multiple Databases, Read/Write Splitting) / Prometheus...
* Каждая функция поставляется с тестами
* Дружественная для разработчиков

## Установка

```sh
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
```

## Быстрый старт

```go
package main

import (
  "gorm.io/gorm"
  "gorm.io/driver/sqlite"
)

type Product struct {
  gorm.Model
  Code  string
  Price uint
}

func main() {
  db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
  if err != nil {
    panic("failed to connect database")
  }

  // Миграция схем
  db.AutoMigrate(&Product{})

  // Создание
  db.Create(&Product{Code: "D42", Price: 100})

  // Чтение
  var product Product
  db.First(&product, 1) // find product with integer primary key
  db.First(&product, "code = ?", "D42") // find product with code D42

  // Обновление - обновить цену товара в 200
  db.Model(&product).Update("Price", 200)
  // Обновление - обновить несколько полей
  db.Model(&product).Updates(Product{Price: 200, Code: "F42"}) // non-zero fields
  db.Model(&product).Updates(map[string]interface{}{"Price": 200, "Code": "F42"})

  // Удаление - удаление товара
  db.Delete(&product, 1)
}
```
