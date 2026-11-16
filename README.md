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
### 2.2 Qual categoria teve mais indicações ao longo da história do Oscar?

```js
db.registros.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
])
```

**R:**

```js
{
  _id: 'DIRECTING',
  total: 471
}
```

---

### 2.3 Qual categoria teve menos indicações ao longo da história?

```js
db.registros.aggregate([
  { $group: { _id: "$categoria", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
])
```

**R:**

```js
{
  _id: 'SPECIAL ACHIEVEMENT AWARD (Sound Editing)',
  total: 1
}
```

---

### 2.4 A partir de que ano a categoria "ACTRESS" deixou de existir? (Dica: procure a última cerimônia com essa categoria)

```js
db.registros.aggregate([
  { $match: { categoria: "ACTRESS" } },
  { $sort: { ano_cerimonia: -1 } },
  { $limit: 1 }
])
```

**R:** 1976

```js
{
  _id: ObjectId('6afa0b2ba8be6a779c08a7fc'),
  id_registro: 5309,
  ano_filmagem: 1975,
  ano_cerimonia: 1976,
  cerimonia: 48,
  categoria: 'ACTRESS',
  nome_do_indicado: 'Isabelle Adjani',
  nome_do_filme: 'The Story of Adele H.',
  vencedor: 0
}
```

---

### 2.5 Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?

```js
db.registros.aggregate([
  {
    $group: {
      _id: "$categoria",
      anos: { $addToSet: "$ano_cerimonia" }
    }
  },
  {
    $match: {
      $and: [
        { anos: 1928 },
        { anos: { $ne: 2026 } }
      ]
    }
  }
])
```

**R:**

- CINEMATOGRAPHY
- UNIQUE AND ARTISTIC PICTURE
- DIRECTING (Comedy Picture)
- WRITING (Adaptation)
- ACTOR
- ART DIRECTION
- WRITING (Original Story)
- ACTRESS
- WRITING (Title Writing)
- OUTSTANDING PICTURE
- DIRECTING (Dramatic Picture)
- ENGINEERING EFFECTS
- SPECIAL AWARD

---

### 2.6 Liste todas as categorias que contêm a palavra "DIRECTING" no nome.

```js
db.registros.aggregate([
  { $match: { categoria: /DIRECTING/ } },
  { $group: { _id: "$categoria" } }
])
```

**R:**

- DIRECTING
- DIRECTING (Comedy Picture)
- DIRECTING (Dramatic Picture)

  ---

## Nível 3: Atores e Atrizes Famosos

### Natalie Portman

#### 3.1 Quantas vezes Natalie Portman foi indicada ao Oscar?

```js
db.registros.countDocuments({ nome_do_indicado: "Natalie Portman" })
```

**R:** 3

---

#### 3.2 Quantos Oscars Natalie Portman ganhou?

```js
db.registros.countDocuments({ nome_do_indicado: "Natalie Portman", vencedor: 1 })
```

**R:** 1

---

#### 3.3 Em quais anos e por quais filmes Natalie Portman foi indicada?

```js
db.registros.find(
  { nome_do_indicado: "Natalie Portman" },
  { _id: 0, ano_cerimonia: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 })
```

**R:**

```js
{
  ano_cerimonia: 2005,
  nome_do_filme: 'Closer'
}
{
  ano_cerimonia: 2011,
  nome_do_filme: 'Black Swan'
}
{
  ano_cerimonia: 2017,
  nome_do_filme: 'Jackie'
}
```

---

#### 3.4 Liste todas as indicações de Natalie Portman mostrando: ano, categoria, filme e se venceu.

```js
db.registros.find(
  { nome_do_indicado: "Natalie Portman" },
  { _id: 0, ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1 }
).sort({ ano_cerimonia: 1 })
```

**R:**

```js
{
  ano_cerimonia: 2005,
  categoria: 'ACTRESS IN A SUPPORTING ROLE',
  nome_do_filme: 'Closer',
  vencedor: 0
}
{
  ano_cerimonia: 2011,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_filme: 'Black Swan',
  vencedor: 1
}
{
  ano_cerimonia: 2017,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_filme: 'Jackie',
  vencedor: 0
}
```

---

### Viola Davis

#### 3.5 Quantas vezes Viola Davis foi indicada ao Oscar?

```js
db.registros.countDocuments({ nome_do_indicado: "Viola Davis" })
```

**R:** 4

---

#### 3.6 Quantos Oscars Viola Davis ganhou?

```js
db.registros.countDocuments({ nome_do_indicado: "Viola Davis", vencedor: 1 })
```

**R:** 1

---

#### 3.7 Por quais filmes Viola Davis foi indicada?

```js
db.registros.distinct("nome_do_filme", { nome_do_indicado: "Viola Davis" })
```

**R:** `[ 'Doubt', 'Fences', "Ma Rainey's Black Bottom", 'The Help' ]`

---

### Amy Adams

#### 3.8 Amy Adams já ganhou algum Oscar?

```js
db.registros.countDocuments({ nome_do_indicado: "Amy Adams", vencedor: 1 })
```

**R:** Não (0)

---

#### 3.9 Quantas vezes Amy Adams foi indicada sem ganhar?

```js
db.registros.countDocuments({ nome_do_indicado: "Amy Adams", vencedor: 0 })
```

**R:** 6

---

### Denzel Washington

#### 3.10 Denzel Washington já ganhou algum Oscar?

```js
db.registros.countDocuments({ nome_do_indicado: "Denzel Washington", vencedor: 1 })
```

**R:** 2

---

#### 3.11 Quantas vezes Denzel Washington foi indicado ao Oscar?

```js
db.registros.countDocuments({ nome_do_indicado: "Denzel Washington" })
```

**R:** 9

---

#### 3.12 Liste todos os Oscars que Denzel Washington ganhou (ano, categoria, filme).

```js
db.registros.find(
  { nome_do_indicado: "Denzel Washington", vencedor: 1 },
  { _id: 0, ano_cerimonia: 1, categoria: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 })
```

**R:**

```js
{
  ano_cerimonia: 1990,
  categoria: 'ACTOR IN A SUPPORTING ROLE',
  nome_do_filme: 'Glory'
}
{
  ano_cerimonia: 2002,
  categoria: 'ACTOR IN A LEADING ROLE',
  nome_do_filme: 'Training Day'
}
```
