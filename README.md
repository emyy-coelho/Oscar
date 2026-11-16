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
---

## Nível 4: Vencedores Históricos

### 4.1 Quem ganhou o primeiro Oscar para Melhor Atriz (ACTRESS)? Em que ano e por qual filme?

```js
db.registros.find(
  { categoria: "ACTRESS", vencedor: 1 },
  { _id: 0, nome_do_indicado: 1, ano_cerimonia: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 }).limit(1)
```

**R:**

```js
{
  ano_cerimonia: 1928,
  nome_do_indicado: 'Janet Gaynor',
  nome_do_filme: '7th Heaven'
}
```

---

### 4.2 Quem ganhou o primeiro Oscar para Melhor Ator (ACTOR)? Em que ano e por qual filme?

```js
db.registros.find(
  { categoria: "ACTOR", vencedor: 1 },
  { _id: 0, nome_do_indicado: 1, ano_cerimonia: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 }).limit(1)
```

**R:**

```js
{
  ano_cerimonia: 1928,
  nome_do_indicado: 'Emil Jannings',
  nome_do_filme: 'The Last Command'
}
```

---

### 4.3 Quantos vencedores existem ao todo na base de dados?

```js
db.registros.countDocuments({ vencedor: 1 })
```

**R:** 2474

---

### 4.4 Liste todos os filmes que ganharam o Oscar de Melhor Filme (categoria "OUTSTANDING PICTURE" ou "BEST PICTURE").

```js
db.registros.find(
  { categoria: { $in: ["OUTSTANDING PICTURE", "BEST PICTURE"] }, vencedor: 1 },
  { _id: 0, ano_cerimonia: 1, categoria: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 })
```

**R:**

- Wings
- The Broadway Melody
- Lawrence of Arabia
- Tom Jones
- My Fair Lady
- The Sound of Music
- A Man for All Seasons
- In the Heat of the Night
- Oliver!
- Midnight Cowboy
- Patton
- The French Connection
- The Godfather
- The Sting
- The Godfather Part II
- One Flew over the Cuckoo's Nest
- Rocky
- Annie Hall
- The Deer Hunter
- Kramer vs. Kramer

---

### 4.5 Quantos filmes diferentes já ganharam o Oscar?

```js
db.registros.distinct("nome_do_filme", { vencedor: 1 }).length
```

**R:** 1336

---

## Nível 5: Análise de Indicações

### 5.1 Quais atores/atrizes foram indicados mais de uma vez? Liste o nome e o número de indicações.

```js
db.registros.aggregate([
  { $match: { categoria: /ACTOR|ACTRESS/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $match: { indicacoes: { $gt: 1 } } },
  { $sort: { indicacoes: -1 } }
])
```

**R:**

| Nome | Indicações |
|------|-----------|
| Meryl Streep | 21 |
| Jack Nicholson | 12 |
| Katharine Hepburn | 12 |
| Bette Davis | 11 |
| Denzel Washington | 9 |
| Spencer Tracy | 9 |
| Laurence Olivier | 9 |
| Paul Newman | 9 |
| Al Pacino | 9 |
| Robert De Niro | 8 |
| Marlon Brando | 8 |
| Peter O'Toole | 8 |
| Glenn Close | 8 |
| Judi Dench | 8 |
| Cate Blanchett | 8 |
| Geraldine Page | 8 |
| Jeff Bridges | 7 |
| Robert Duvall | 7 |
| Kate Winslet | 7 |

---

### 5.2 Qual ator ou atriz tem o maior número de indicações na história do Oscar?

```js
db.registros.aggregate([
  { $match: { categoria: /ACTOR|ACTRESS/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $sort: { indicacoes: -1 } },
  { $limit: 1 }
])
```

**R:**

```js
{
  _id: 'Meryl Streep',
  indicacoes: 21
}
```

---

### 5.3 Quais atores foram indicados mais de 3 vezes, mas nunca ganharam?

```js
db.registros.aggregate([
  { $match: { categoria: /ACTOR|ACTRESS/ } },
  {
    $group: {
      _id: "$nome_do_indicado",
      indicacoes: { $sum: 1 },
      vitorias: { $sum: "$vencedor" }
    }
  },
  { $match: { indicacoes: { $gt: 3 }, vitorias: 0 } },
  { $sort: { indicacoes: -1 } }
])
```

**R:**

- Peter O'Toole
- Glenn Close
- Richard Burton
- Amy Adams
- Deborah Kerr
- Thelma Ritter
- Bradley Cooper
- Annette Bening
- Arthur Kennedy
- Irene Dunne
- Albert Finney
- Michelle Williams
- Mickey Rooney
- Ed Harris
- Mark Ruffalo
- Jane Alexander
- Rosalind Russell
- Barbara Stanwyck
- Agnes Moorehead
- Montgomery Clift

---

### 5.4 Encontre todos os artistas que foram indicados em categorias diferentes (ex: ator e diretor).

```js
db.registros.aggregate([
  {
    $group: {
      _id: "$nome_do_indicado",
      categorias: { $addToSet: "$categoria" }
    }
  },
  { $match: { "categorias.1": { $exists: true } } },
  { $project: { _id: 1, categorias: 1, total_categorias: { $size: "$categorias" } } },
  { $sort: { total_categorias: -1 } }
])
```

**R:**

- Metro-Goldwyn-Mayer
- Warner Bros.
- Paramount
- RKO Radio
- Alex North
- Alfred Newman
- Walt Disney, Producer
- Victor Young
- National Film Board of Canada
- 20th Century-Fox
- Franz Waxman
- Robert Benton
- Paul Newman
- John Williams
- Ingmar Bergman
- Jerry Goldsmith
- Elmer Bernstein
- George Seaton
- Herbert Stothart
- Paddy Chayefsky

---

### 5.5 Quantos indicados têm exatamente 1 indicação na história?

```js
db.registros.aggregate([
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $match: { indicacoes: 1 } },
  { $count: "total_com_1_indicacao" }
])
```

**R:**

```js
{ total_com_1_indicacao: 5594 }
```

---

### 5.6 Qual o maior número de indicados em um único ano? Essa é uma pergunta franca.

```js
db.registros.aggregate([
  { $group: { _id: "$ano_cerimonia", total_indicados: { $sum: 1 } } },
  { $sort: { total_indicados: -1 } },
  { $limit: 1 }
])
```

**R:**

```js
{
  _id: 1943,
  total_indicados: 186
}
```

# Exercícios MongoDB: Indicados ao Oscar

Coleção utilizada: `registros`

---

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
  // ... demais indicados de 2025 (ids 10891 a 10897)
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
  // ... demais indicados de 2026
])
```

**R:** *(registros inseridos conforme acima)*

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

---

## Nível 4: Vencedores Históricos

### 4.1 Quem ganhou o primeiro Oscar para Melhor Atriz (ACTRESS)? Em que ano e por qual filme?

```js
db.registros.find(
  { categoria: "ACTRESS", vencedor: 1 },
  { _id: 0, nome_do_indicado: 1, ano_cerimonia: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 }).limit(1)
```

**R:**

```js
{
  ano_cerimonia: 1928,
  nome_do_indicado: 'Janet Gaynor',
  nome_do_filme: '7th Heaven'
}
```

---

### 4.2 Quem ganhou o primeiro Oscar para Melhor Ator (ACTOR)? Em que ano e por qual filme?

```js
db.registros.find(
  { categoria: "ACTOR", vencedor: 1 },
  { _id: 0, nome_do_indicado: 1, ano_cerimonia: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 }).limit(1)
```

**R:**

```js
{
  ano_cerimonia: 1928,
  nome_do_indicado: 'Emil Jannings',
  nome_do_filme: 'The Last Command'
}
```

---

### 4.3 Quantos vencedores existem ao todo na base de dados?

```js
db.registros.countDocuments({ vencedor: 1 })
```

**R:** 2474

---

### 4.4 Liste todos os filmes que ganharam o Oscar de Melhor Filme (categoria "OUTSTANDING PICTURE" ou "BEST PICTURE").

```js
db.registros.find(
  { categoria: { $in: ["OUTSTANDING PICTURE", "BEST PICTURE"] }, vencedor: 1 },
  { _id: 0, ano_cerimonia: 1, categoria: 1, nome_do_filme: 1 }
).sort({ ano_cerimonia: 1 })
```

**R:**

- Wings
- The Broadway Melody
- Lawrence of Arabia
- Tom Jones
- My Fair Lady
- The Sound of Music
- A Man for All Seasons
- In the Heat of the Night
- Oliver!
- Midnight Cowboy
- Patton
- The French Connection
- The Godfather
- The Sting
- The Godfather Part II
- One Flew over the Cuckoo's Nest
- Rocky
- Annie Hall
- The Deer Hunter
- Kramer vs. Kramer

---

### 4.5 Quantos filmes diferentes já ganharam o Oscar?

```js
db.registros.distinct("nome_do_filme", { vencedor: 1 }).length
```

**R:** 1336

---

## Nível 5: Análise de Indicações

### 5.1 Quais atores/atrizes foram indicados mais de uma vez? Liste o nome e o número de indicações.

```js
db.registros.aggregate([
  { $match: { categoria: /ACTOR|ACTRESS/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $match: { indicacoes: { $gt: 1 } } },
  { $sort: { indicacoes: -1 } }
])
```

**R:**

| Nome | Indicações |
|------|-----------|
| Meryl Streep | 21 |
| Jack Nicholson | 12 |
| Katharine Hepburn | 12 |
| Bette Davis | 11 |
| Denzel Washington | 9 |
| Spencer Tracy | 9 |
| Laurence Olivier | 9 |
| Paul Newman | 9 |
| Al Pacino | 9 |
| Robert De Niro | 8 |
| Marlon Brando | 8 |
| Peter O'Toole | 8 |
| Glenn Close | 8 |
| Judi Dench | 8 |
| Cate Blanchett | 8 |
| Geraldine Page | 8 |
| Jeff Bridges | 7 |
| Robert Duvall | 7 |
| Kate Winslet | 7 |

---

### 5.2 Qual ator ou atriz tem o maior número de indicações na história do Oscar?

```js
db.registros.aggregate([
  { $match: { categoria: /ACTOR|ACTRESS/ } },
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $sort: { indicacoes: -1 } },
  { $limit: 1 }
])
```

**R:**

```js
{
  _id: 'Meryl Streep',
  indicacoes: 21
}
```

---

### 5.3 Quais atores foram indicados mais de 3 vezes, mas nunca ganharam?

```js
db.registros.aggregate([
  { $match: { categoria: /ACTOR|ACTRESS/ } },
  {
    $group: {
      _id: "$nome_do_indicado",
      indicacoes: { $sum: 1 },
      vitorias: { $sum: "$vencedor" }
    }
  },
  { $match: { indicacoes: { $gt: 3 }, vitorias: 0 } },
  { $sort: { indicacoes: -1 } }
])
```

**R:**

- Peter O'Toole
- Glenn Close
- Richard Burton
- Amy Adams
- Deborah Kerr
- Thelma Ritter
- Bradley Cooper
- Annette Bening
- Arthur Kennedy
- Irene Dunne
- Albert Finney
- Michelle Williams
- Mickey Rooney
- Ed Harris
- Mark Ruffalo
- Jane Alexander
- Rosalind Russell
- Barbara Stanwyck
- Agnes Moorehead
- Montgomery Clift

---

### 5.4 Encontre todos os artistas que foram indicados em categorias diferentes (ex: ator e diretor).

```js
db.registros.aggregate([
  {
    $group: {
      _id: "$nome_do_indicado",
      categorias: { $addToSet: "$categoria" }
    }
  },
  { $match: { "categorias.1": { $exists: true } } },
  { $project: { _id: 1, categorias: 1, total_categorias: { $size: "$categorias" } } },
  { $sort: { total_categorias: -1 } }
])
```

**R:**

- Metro-Goldwyn-Mayer
- Warner Bros.
- Paramount
- RKO Radio
- Alex North
- Alfred Newman
- Walt Disney, Producer
- Victor Young
- National Film Board of Canada
- 20th Century-Fox
- Franz Waxman
- Robert Benton
- Paul Newman
- John Williams
- Ingmar Bergman
- Jerry Goldsmith
- Elmer Bernstein
- George Seaton
- Herbert Stothart
- Paddy Chayefsky

---

### 5.5 Quantos indicados têm exatamente 1 indicação na história?

```js
db.registros.aggregate([
  { $group: { _id: "$nome_do_indicado", indicacoes: { $sum: 1 } } },
  { $match: { indicacoes: 1 } },
  { $count: "total_com_1_indicacao" }
])
```

**R:**

```js
{ total_com_1_indicacao: 5594 }
```

---

### 5.6 Qual o maior número de indicados em um único ano? Essa é uma pergunta franca.

```js
db.registros.aggregate([
  { $group: { _id: "$ano_cerimonia", total_indicados: { $sum: 1 } } },
  { $sort: { total_indicados: -1 } },
  { $limit: 1 }
])
```

**R:**

```js
{
  _id: 1943,
  total_indicados: 186
}
```

---

## Nível 6: Análise de Filmes

### Toy Story

#### 6.1 A série de filmes Toy Story ganhou Oscars em quais anos?

```js
db.registros.distinct("ano_cerimonia", { nome_do_filme: /Toy Story/, vencedor: 1 })
```

**R:** `[ 2011, 2020 ]`

---

#### 6.2 Quantas indicações a franquia Toy Story recebeu no total?

```js
db.registros.countDocuments({ nome_do_filme: /Toy Story/ })
```

**R:** 11

---

#### 6.3 Em quais categorias os filmes Toy Story foram indicados?

```js
db.registros.distinct("categoria", { nome_do_filme: /Toy Story/ })
```

**R:**

- ANIMATED FEATURE FILM
- BEST PICTURE
- MUSIC (Original Musical or Comedy Score)
- MUSIC (Original Song)
- SOUND EDITING
- WRITING (Adapted Screenplay)
- WRITING (Screenplay Written Directly for the Screen)

---

### Crash

#### 6.4 Em qual edição do Oscar o filme "Crash" concorreu?

```js
db.registros.distinct("cerimonia", { nome_do_filme: "Crash" })
```

**R:** 78

---

#### 6.5 Quantas indicações o filme "Crash" recebeu?

```js
db.registros.countDocuments({ nome_do_filme: "Crash" })
```

**R:** 6

---

#### 6.6 "Crash" ganhou o Oscar de Melhor Filme?

```js
db.registros.find(
  { nome_do_filme: "Crash", categoria: "BEST PICTURE" },
  { _id: 0, ano_cerimonia: 1, categoria: 1, nome_do_filme: 1, vencedor: 1 }
)
```

**R:** Sim

```js
{
  ano_cerimonia: 2006,
  categoria: 'BEST PICTURE',
  nome_do_filme: 'Crash',
  vencedor: 1
}
```

---

### Central do Brasil

#### 6.7 O filme "Central do Brasil" aparece no banco de dados?

```js
db.registros.find({ nome_do_filme: /Central do Brasil|Central Station/i })
```

**R:** Sim

---

#### 6.8 Se sim, quantas indicações "Central do Brasil" recebeu?

```js
db.registros.countDocuments({ nome_do_filme: /Central do Brasil|Central Station/i })
```

**R:** 2

---

## Nível 7: Operações de Atualização

### 7.1 No campo "vencedor", altere todos os valores "true" (string) para true (booleano) e "false" (string) para false (booleano).

```js
db.registros.updateMany(
  { vencedor: 1 },
  { $set: { vencedor: true } }
)

db.registros.updateMany(
  { vencedor: 0 },
  { $set: { vencedor: false } }
)
```

**R:**

```js
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 8426,
  modifiedCount: 8426,
  upsertedCount: 0
}
```

---

### 7.2 Inclua no banco 3 filmes que nunca foram nomeados ao Oscar, mas que você acha que merecem. Use dados fictícios mas realistas.

```js
db.registros.insertMany([
  {
    id_registro: 99901,
    ano_filmagem: 2004,
    ano_cerimonia: 2006,
    cerimonia: 78,
    categoria: "BEST PICTURE",
    nome_do_indicado: "Toshio Suzuki",
    nome_do_filme: "O Castelo Animado",
    vencedor: false
  },
  {
    id_registro: 99902,
    ano_filmagem: 2008,
    ano_cerimonia: 2010,
    cerimonia: 82,
    categoria: "ANIMATED FEATURE FILM",
    nome_do_indicado: "Hayao Miyazaki",
    nome_do_filme: "Ponyo",
    vencedor: false
  },
  {
    id_registro: 99903,
    ano_filmagem: 2001,
    ano_cerimonia: 2003,
    cerimonia: 75,
    categoria: "BEST PICTURE",
    nome_do_indicado: "Toshio Suzuki",
    nome_do_filme: "A Viagem de Chihiro",
    vencedor: false
  }
])
```

**R:**

```js
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('6afb69cac0e64866c5e6411d'),
    '1': ObjectId('6afb69cac0e64866c5e6411e'),
    '2': ObjectId('6afb69cac0e64866c5e6411f')
  }
}
```

---

### 7.3 Adicione uma nova categoria chamada "BEST INTERNATIONAL FEATURE FILM" com alguns vencedores recentes (2020-2024).

```js
db.registros.insertMany([
  {
    ano_filmagem: 2019,
    ano_cerimonia: 2020,
    cerimonia: 92,
    categoria: "BEST INTERNATIONAL FEATURE FILM",
    nome_do_indicado: "South Korea",
    nome_do_filme: "Parasite",
    vencedor: true
  },
  {
    ano_filmagem: 2020,
    ano_cerimonia: 2021,
    cerimonia: 93,
    categoria: "BEST INTERNATIONAL FEATURE FILM",
    nome_do_indicado: "Denmark",
    nome_do_filme: "Another Round",
    vencedor: true
  },
  {
    ano_filmagem: 2021,
    ano_cerimonia: 2022,
    cerimonia: 94,
    categoria: "BEST INTERNATIONAL FEATURE FILM",
    nome_do_indicado: "Japan",
    nome_do_filme: "Drive My Car",
    vencedor: true
  },
  {
    ano_filmagem: 2022,
    ano_cerimonia: 2023,
    cerimonia: 95,
    categoria: "BEST INTERNATIONAL FEATURE FILM",
    nome_do_indicado: "Germany",
    nome_do_filme: "All Quiet on the Western Front",
    vencedor: true
  },
  {
    ano_filmagem: 2023,
    ano_cerimonia: 2024,
    cerimonia: 96,
    categoria: "BEST INTERNATIONAL FEATURE FILM",
    nome_do_indicado: "United Kingdom",
    nome_do_filme: "The Zone of Interest",
    vencedor: true
  }
])
```

**R:**

```js
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('6afb08c95387205688bb7240'),
    '1': ObjectId('6afb08c95387205688bb7241'),
    '2': ObjectId('6afb08c95387205688bb7242'),
    '3': ObjectId('6afb08c95387205688bb7243'),
    '4': ObjectId('6afb08c95387205688bb7244')
  }
}
```

---

### 7.4 Corrija possíveis erros de digitação nos nomes dos filmes (ex: espaços extras, caracteres especiais desnecessários).

```js
db.registros.updateMany(
  { nome_do_filme: { $type: "string" } },
  [
    {
      $set: {
        nome_do_filme: { $trim: { input: "$nome_do_filme" } }
      }
    }
  ]
)
```

**R:**

```js
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 10909,
  modifiedCount: 0,
  upsertedCount: 0
}
```

---

### 7.5 Remova todos os registros com valor NULL no campo nome_do_filme.

```js
db.registros.deleteMany({ nome_do_filme: null })
```

**R:**

```js
{
  acknowledged: true,
  deletedCount: 0
}
```
