# Exercícios MongoDB: Indicados ao Oscar

## Nível 1: Primeiros Passos

### 1.1 Quantos registros existem na coleção de indicados ao Oscar?

```js
db.registros.countDocuments()
```

**R:** 10889

---

### 1.2 Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.

```js
db.registros.distinct("categoria").length
```

**R:** 115

---

### 1.3 Qual foi o primeiro ano de cerimônia do Oscar registrado na base?

```js
db.registros.find().skip(15).limit(1).sort({ ano_cerimonia: 1 })
```

**R:** 1928

---

### 1.4 Qual foi o último ano de cerimônia registrado na base?

```js
db.registros.find().skip(15).limit(1).sort({ ano_cerimonia: -1 })
```

**R:** 2024

---

### 1.5 Quantas cerimônias do Oscar estão registradas no total?

```js
db.registros.distinct("cerimonia").length
```

**R:** 96

---

### 1.6 Atualize os registros da coleção com os dados do Oscar 2025 e 2026 (pesquise os indicados e adicione-os).
**R:**
```js
db.registros.insertMany([
  {
    id_registro: 10890,
    ano_filmagem: 2024,
    ano_cerimonia: 2025,
    cerimonia: 97,
    categoria: "BEST PICTURE",
    nome_do_indicado: "Alex Coco, Samantha Quan and Sean Baker, Producers",
    nome_do_filme: "Anora",
    vencedor: 1
  },
  {
    id_registro: 10898,
    ano_filmagem: 2025,
    ano_cerimonia: 2026,
    cerimonia: 98,
    categoria: "DIRECTING",
    nome_do_indicado: "Paul Thomas Anderson",
    nome_do_filme: "One Battle After Another",
    vencedor: 1
  }
])
```
---

## Nível 2: Explorando Categorias

### 2.1 Quantas indicações existem para cada categoria? Agrupe por categoria e ordene da mais frequente para a menos frequente.

```js
db.registros.aggregate([
  {
    $group: {
      _id: "$categoria",
      total: { $sum: 1 }
    }
  },
  {
    $sort: {
      total: -1
    }
  }
])
```

**R:**

| Categoria | Total |
|-----------|-------|
| DIRECTING | 471 |
| FILM EDITING | 450 |
| ACTRESS IN A SUPPORTING ROLE | 440 |
| ACTOR IN A SUPPORTING ROLE | 440 |
| DOCUMENTARY (Short Subject) | 378 |
| BEST PICTURE | 374 |
| DOCUMENTARY (Feature) | 345 |
| CINEMATOGRAPHY | 338 |
| FOREIGN LANGUAGE FILM | 315|
| ART DIRECTION | 307|
| COSTUME DESIGN | 295 |
| MUSIC (Original Score) | 270 |
| SOUND | 245 |
| ACTRESS IN A LEADING ROLE | 243 |
| ACTOR IN A LEADING ROLE | 242 |
| ACTRESS | 236 |
| MUSIC (Original Song) | 235 |
| ACTOR | 232 |
| SHORT FILM (Live Action) | 226 |
| MUSIC (Song) | 215 |
---

