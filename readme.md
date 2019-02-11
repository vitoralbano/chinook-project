# Projeto Chinook
# Perguntas:
1. Conjunto de perguntas 01
    1. [Quais países possuem mais faturas?](#11-Quais-países-possuem-mais-faturas?)
    2. [Qual cidade tem os melhores clientes?](#12-Qual-cidade-tem-os-melhores-clientes)
    3. [Quem é o melhor cliente?](#13-Quem-é-o-melhor-cliente?)
2. Conjunto de perguntas 02
    1. [Retorne o e-mail, nome, sobrenome e gênero de todos os ouvintes de Rock. Retorne sua lista ordenada alfabeticamente por endereço de e-mail, começando por A.](#21-Retorne-o-e-mail,-nome,-sobrenome-e-gênero-de-todos-os-ouvintes-de-Rock)
    2. [Agora que sabemos que nossos clientes amam rock, podemos decidir quais músicos convidar para tocar no show](#22-Agora-que-sabemos-que-nossos-clientes-amam-rock,-podemos-decidir-quais-músicos-convidar-para-tocar-no-show)
    3.
        1. [Primeiro, descubra qual artista ganhou mais de acordo com InvoiceLines](#231-Primeiro,-descubra-qual-artista-ganhou-mais-de-acordo-com-InvoiceLines)
        2. [Agora encontre qual cliente gastou mais com o artista que você encontrou acima](#232-Agora-encontre-qual-cliente-gastou-mais-com-o-artista-que-você-encontrou-acima)
3. Conjunto de perguntas 03
    1. [Queremos descobrir o gênero musical mais popular em cada país](#31-Queremos-descobrir-o-gênero-musical-mais-popular-em-cada-país)
    2. [Retorne todos os nomes de músicas que possuem um comprimento de canção maior que o comprimento médio de canção.](#32-Retorne-todos-os-nomes-de-músicas-que-possuem-um-comprimento-de-canção-maior-que-o-comprimento-médio-de-canção)
    3. [Qual cliente gastou mais em músicas por país.](#33-Qual-cliente-gastou-mais-em-músicas-por-país)
4. Extra questions
    1. [Quais são os gêneros musicais mais vendidos?](#41-Quais-são-os-gêneros-musicais-mais-vendidos?)
    2. [Qual é o volume de vendas do gênero Rock por país?](#42-Qual-é-o-volume-de-vendas-do-gênero-Rock-por-país)
    3. [Média de vendas do gênero musical Rock, por trimestre](#43-Média-de-vendas-do-gênero-musical-Rock-por-trimestre)

# Diagrama de Entidade e Relacionamento (ERD)
![Chinook ERD](db/chinook-erd.png)

# Respostas
## 1.1. Quais países possuem mais faturas?

Query:

```sql
SELECT 
    BillingCountry,
    COUNT(*) Invoices
FROM Invoice
    GROUP BY BillingCountry
ORDER BY Invoices DESC;
```
Result:

| BillingCountry   | Invoices |
| :---             | ---:    |
| "USA"            | "91"     |
| "Canada"         | "56"     |
| "Brazil"         | "35"     |
| "France"         | "35"     |
| "Germany"        | "28"     |
| "United Kingdom" | "21"     |
| "Czech Republic" | "14"     |
| "Portugal"       | "14"     |
| "India"          | "13"     |
| "Argentina"      | "7"      |
| "Australia"      | "7"      |
| "Austria"        | "7"      |
| "Belgium"        | "7"      |
| "Chile"          | "7"      |
| "Denmark"        | "7"      |
| "Finland"        | "7"      |
| "Hungary"        | "7"      |
| "Ireland"        | "7"      |
| "Italy"          | "7"      |
| "Netherlands"    | "7"      |
| "Norway"         | "7"      |
| "Poland"         | "7"      |
| "Spain"          | "7"      |
| "Sweden"         | "7"      |

```sql
-- 24 rows returned
```

## 1.2. Qual cidade tem os melhores clientes?

Query:
```sql
SELECT Inv.BillingCity,
    MAX(Inv.Total) MaxInvoice
FROM 
    (SELECT BillingCity,
        SUM(Total) Total
	FROM Invoice
    GROUP BY BillingCity
    ORDER BY Total) Inv
```

Result:

| BillingCity | MaxInvoice |
| :---        | :---:      |
| "Prague"    | "90.24"    |

```sql
-- 1 rows returned
```

## 1.3. Quem é o melhor cliente?

Query:
```sql
SELECT 
    Cus.CustomerId,
    Cus.FirstName,
    Cus.Country,
    Cus_Profit.Total TotalProfit
FROM 
    (SELECT 
        Inv.CustomerId,
        SUM(Inv.Total) Total
    FROM Invoice Inv
    GROUP BY Inv.CustomerId
    ORDER BY 2 DESC LIMIT 1) Cus_Profit
JOIN Customer Cus ON Cus.CustomerId = Cus_Profit.CustomerId;
```

Result:

| CustomerId | FirstName | Country          | TotalProfit |
| :---:      | :---      | :---             | ---:       |
| "6"        | "Helena"  | "Czech Republic" | "49.62"     |

```sql
-- 1 rows returned
```

## 2.1. Retorne o e-mail, nome, sobrenome e gênero de todos os ouvintes de Rock.
Retorne sua lista ordenada alfabeticamente por endereço de e-mail, começando por A.
>Você consegue encontrar um jeito de lidar com e-mails duplicados para que ninguém receba vários e-mails?

Query:
```sql
SELECT
    cus.Email,
    cus.FirstName,
    cus.LastName,
    gen.Name Genre
FROM Customer cus
JOIN Invoice inv ON inv.CustomerId = cus.CustomerId
JOIN InvoiceLine invLine ON invLine.InvoiceId = inv.InvoiceId
JOIN Track tra ON tra.TrackId = invLine.TrackId
JOIN Genre gen ON gen.GenreId = tra.GenreId

WHERE invLine.TrackId IN 
    (SELECT t.TrackId
    FROM Track t
    JOIN Genre gen ON gen.GenreId = t.GenreId AND gen.Name = 'Rock')
GROUP BY cus.Email
ORDER BY cus.Email;
```

Result: 

| Email                           | FirstName   | LastName       | Genre  |
| :---                            | :---        | :---           | :---:   |
| "aaronmitchell@yahoo.ca"        | "Aaron"     | "Mitchell"     | "Rock" |
| "alero@uol.com.br"              | "Alexandre" | "Rocha"        | "Rock" |
| "astrid.gruber@apple.at"        | "Astrid"    | "Gruber"       | "Rock" |
| "bjorn.hansen@yahoo.no"         | "Bjørn"     | "Hansen"       | "Rock" |
| "camille.bernard@yahoo.fr"      | "Camille"   | "Bernard"      | "Rock" |
| "daan_peeters@apple.be"         | "Daan"      | "Peeters"      | "Rock" |
| "diego.gutierrez@yahoo.ar"      | "Diego"     | "Gutiérrez"    | "Rock" |
| "dmiller@comcast.com"           | "Dan"       | "Miller"       | "Rock" |
| "dominiquelefebvre@gmail.com"   | "Dominique" | "Lefebvre"     | "Rock" |
| "edfrancis@yachoo.ca"           | "Edward"    | "Francis"      | "Rock" |
| "eduardo@woodstock.com.br"      | "Eduardo"   | "Martins"      | "Rock" |
| "ellie.sullivan@shaw.ca"        | "Ellie"     | "Sullivan"     | "Rock" |
| "emma_jones@hotmail.com"        | "Emma"      | "Jones"        | "Rock" |
| "enrique_munoz@yahoo.es"        | "Enrique"   | "Muñoz"        | "Rock" |
| "fernadaramos4@uol.com.br"      | "Fernanda"  | "Ramos"        | "Rock" |
| "fharris@google.com"            | "Frank"     | "Harris"       | "Rock" |
| "fralston@gmail.com"            | "Frank"     | "Ralston"      | "Rock" |
| "frantisekw@jetbrains.com"      | "František" | "Wichterlová"  | "Rock" |
| "ftremblay@gmail.com"           | "François"  | "Tremblay"     | "Rock" |
| "fzimmermann@yahoo.de"          | "Fynn"      | "Zimmermann"   | "Rock" |
| "hannah.schneider@yahoo.de"     | "Hannah"    | "Schneider"    | "Rock" |
| "hholy@gmail.com"               | "Helena"    | "Holý"         | "Rock" |
| "hleacock@gmail.com"            | "Heather"   | "Leacock"      | "Rock" |
| "hughoreilly@apple.ie"          | "Hugh"      | "O'Reilly"     | "Rock" |
| "isabelle_mercier@apple.fr"     | "Isabelle"  | "Mercier"      | "Rock" |
| "jacksmith@microsoft.com"       | "Jack"      | "Smith"        | "Rock" |
| "jenniferp@rogers.ca"           | "Jennifer"  | "Peterson"     | "Rock" |
| "jfernandes@yahoo.pt"           | "João"      | "Fernandes"    | "Rock" |
| "joakim.johansson@yahoo.se"     | "Joakim"    | "Johansson"    | "Rock" |
| "johavanderberg@yahoo.nl"       | "Johannes"  | "Van der Berg" | "Rock" |
| "johngordon22@yahoo.com"        | "John"      | "Gordon"       | "Rock" |
| "jubarnett@gmail.com"           | "Julia"     | "Barnett"      | "Rock" |
| "kachase@hotmail.com"           | "Kathy"     | "Chase"        | "Rock" |
| "kara.nielsen@jubii.dk"         | "Kara"      | "Nielsen"      | "Rock" |
| "ladislav_kovacs@apple.hu"      | "Ladislav"  | "Kovács"       | "Rock" |
| "leonekohler@surfeu.de"         | "Leonie"    | "Köhler"       | "Rock" |
| "lucas.mancini@yahoo.it"        | "Lucas"     | "Mancini"      | "Rock" |
| "luisg@embraer.com.br"          | "Luís"      | "Gonçalves"    | "Rock" |
| "luisrojas@yahoo.cl"            | "Luis"      | "Rojas"        | "Rock" |
| "manoj.pareek@rediff.com"       | "Manoj"     | "Pareek"       | "Rock" |
| "marc.dubois@hotmail.com"       | "Marc"      | "Dubois"       | "Rock" |
| "mark.taylor@yahoo.au"          | "Mark"      | "Taylor"       | "Rock" |
| "marthasilk@gmail.com"          | "Martha"    | "Silk"         | "Rock" |
| "masampaio@sapo.pt"             | "Madalena"  | "Sampaio"      | "Rock" |
| "michelleb@aol.com"             | "Michelle"  | "Brooks"       | "Rock" |
| "mphilips12@shaw.ca"            | "Mark"      | "Philips"      | "Rock" |
| "nschroder@surfeu.de"           | "Niklas"    | "Schröder"     | "Rock" |
| "patrick.gray@aol.com"          | "Patrick"   | "Gray"         | "Rock" |
| "phil.hughes@gmail.com"         | "Phil"      | "Hughes"       | "Rock" |
| "puja_srivastava@yahoo.in"      | "Puja"      | "Srivastava"   | "Rock" |
| "ricunningham@hotmail.com"      | "Richard"   | "Cunningham"   | "Rock" |
| "robbrown@shaw.ca"              | "Robert"    | "Brown"        | "Rock" |
| "roberto.almeida@riotur.gov.br" | "Roberto"   | "Almeida"      | "Rock" |
| "stanisław.wójcik@wp.pl"        | "Stanisław" | "Wójcik"       | "Rock" |
| "steve.murray@yahoo.uk"         | "Steve"     | "Murray"       | "Rock" |
| "terhi.hamalainen@apple.fi"     | "Terhi"     | "Hämäläinen"   | "Rock" |
| "tgoyer@apple.com"              | "Tim"       | "Goyer"        | "Rock" |
| "vstevens@yahoo.com"            | "Victor"    | "Stevens"      | "Rock" |
| "wyatt.girard@yahoo.fr"         | "Wyatt"     | "Girard"       | "Rock" |

```sql
-- 59 rows returned
```
## 2.2. Agora que sabemos que nossos clientes amam rock, podemos decidir quais músicos convidar para tocar no show.

> Vamos convidar os artistas que mais escreveram as músicas de rock em nosso banco de dados. Escreva uma consulta que retorna o nome do Artist (artista) e a contagem total de músicas das dez melhores bandas de rock.

Query:
```sql
SELECT art.Name ArtistName,
    COUNT(*) TrackCount
FROM Track tra
JOIN Album alb ON alb.AlbumId = tra.AlbumId
JOIN Artist art ON art.ArtistId = alb.ArtistId
JOIN Genre gen ON gen.GenreId = tra.GenreId AND gen.Name = 'Rock'
GROUP BY 1
ORDER BY TrackCount DESC
LIMIT 10;
```

Result:

| ArtistName                     | TrackCount |
| :---                           | ---:      |
| "Led Zeppelin"                 | "114"      |
| "U2"                           | "112"      |
| "Deep Purple"                  | "92"       |
| "Iron Maiden"                  | "81"       |
| "Pearl Jam"                    | "54"       |
| "Van Halen"                    | "52"       |
| "Queen"                        | "45"       |
| "The Rolling Stones"           | "41"       |
| "Creedence Clearwater Revival" | "40"       |
| "Kiss"                         | "35"       |

```sql
-- 10 rows returned
```
## 2.3.1. Primeiro, descubra qual artista ganhou mais de acordo com InvoiceLines

Query:
```sql
SELECT art.ArtistId,
    art.Name ArtistName,
    SUM(invLine.UnitPrice * invLine.Quantity) AS Total
FROM InvoiceLine invLine
JOIN Track tra ON tra.TrackId = invLine.TrackId
JOIN Album alb ON alb.AlbumId = tra.AlbumId
JOIN Artist art ON art.ArtistId = alb.ArtistId
GROUP BY art.Name
ORDER BY Total DESC
LIMIT 5;
```

Result: 

| ArtistId | ArtistName     | Total              |
| :---:    | :---           | ---:              |
| "90"     | "Iron Maiden"  | "138.6"            |
| "150"    | "U2"           | "105.93"           |
| "50"     | "Metallica"    | "90.0899999999999" |
| "22"     | "Led Zeppelin" | "86.1299999999999" |
| "149"    | "Lost"         | "81.59"            |

```sql
-- 5 rows returned
```

## 2.3.2 Agora encontre qual cliente gastou mais com o artista que você encontrou acima
Query:
```sql
SELECT cus.CustomerId,
    cus.FirstName,
    cus.LastName,
    SUM(invLine.UnitPrice * invLine.Quantity) AS TotalSpent
FROM InvoiceLine invLine
JOIN Invoice inv ON inv.InvoiceId = invLine.InvoiceId
JOIN Customer cus ON cus.CustomerId = inv.CustomerId
JOIN Track tra ON tra.TrackId = invLine.TrackId
JOIN Album alb ON alb.AlbumId = tra.AlbumId AND alb.ArtistId = 90
GROUP BY cus.CustomerId,
    cus.Email,
    cus.FirstName
ORDER BY TotalSpent DESC
LIMIT 6;
```

Result:

| CustomerId | FirstName   | LastName      | TotalSpent |
| :---:      | :---        | :---          | ---:       |
| "55"       | "Mark"      | "Taylor"      | "17.82"    |
| "35"       | "Madalena"  | "Sampaio"     | "15.84"    |
| "16"       | "Frank"     | "Harris"      | "13.86"    |
| "36"       | "Hannah"    | "Schneider"   | "13.86"    |
| "5"        | "František" | "Wichterlová" | "8.91"     |
| "27"       | "Patrick"   | "Gray"        | "8.91"     |

```sql
-- 6 rows returned
```

## 3.1. Queremos descobrir o gênero musical mais popular em cada país. 
> Determinamos o gênero mais popular como o gênero com o maior número de compras. Escreva uma consulta que retorna cada país juntamente a seu gênero mais vendido. Para países onde o número máximo de compras é compartilhado retorne todos os gêneros.

Query: 
 ```sql
SELECT 
    genData.Purchases,
    genData.BillingCountry Country,
    genData.GenreName 
FROM
    (SELECT inv.BillingCountry,
        gen.Name GenreName,
        COUNT(*) AS Purchases
    FROM Invoice inv
    JOIN InvoiceLine invLine ON invLine.InvoiceId = inv.InvoiceId
    JOIN Track tra ON tra.TrackId = invLine.TrackId
    JOIN Genre gen ON gen.GenreId = tra.GenreId
    GROUP BY inv.BillingCountry, GenreName
    ORDER BY 1, 3 DESC) AS genData
	
JOIN 
    (SELECT genCount.BillingCountry,
        MAX(genCount.Purchases) GenreMax
    FROM 
        (SELECT inv.BillingCountry,
            gen.Name GenreName,
            COUNT(*) AS Purchases
        FROM Invoice inv
        JOIN InvoiceLine invLine ON invLine.InvoiceId = inv.InvoiceId
        JOIN Track tra ON tra.TrackId = invLine.TrackId
        JOIN Genre gen ON gen.GenreId = tra.GenreId
        GROUP BY inv.BillingCountry, GenreName
        ORDER BY 1, 3 DESC) genCount
        GROUP BY 1) genMax
        
ON genMax.BillingCountry = genData.BillingCountry AND genData.Purchases = genMax.GenreMax
ORDER BY Country;
```
Result:

| Purchases  | Country          | GenreName            |
| :---:      | :---             | ---:                 |
| "9"        | "Argentina"      | "Alternative & Punk" |
| "9"        | "Argentina"      | "Rock"               |
| "22"       | "Australia"      | "Rock"               |
| "15"       | "Austria"        | "Rock"               |
| "21"       | "Belgium"        | "Rock"               |
| "81"       | "Brazil"         | "Rock"               |
| "107"      | "Canada"         | "Rock"               |
| "9"        | "Chile"          | "Rock"               |
| "25"       | "Czech Republic" | "Rock"               |
| "21"       | "Denmark"        | "Rock"               |
| "18"       | "Finland"        | "Rock"               |
| "65"       | "France"         | "Rock"               |
| "62"       | "Germany"        | "Rock"               |
| "11"       | "Hungary"        | "Rock"               |
| "25"       | "India"          | "Rock"               |
| "12"       | "Ireland"        | "Rock"               |
| "18"       | "Italy"          | "Rock"               |
| "18"       | "Netherlands"    | "Rock"               |
| "17"       | "Norway"         | "Rock"               |
| "22"       | "Poland"         | "Rock"               |
| "31"       | "Portugal"       | "Rock"               |
| "22"       | "Spain"          | "Rock"               |
| "12"       | "Sweden"         | "Latin"              |
| "157"      | "USA"            | "Rock"               |
| "37"       | "United Kingdom" | "Rock"               |

```sql
-- 25 rows returned
```
![Chinook ERD](db/chinook-erd.png)

## 3.2. Retorne todos os nomes de músicas que possuem um comprimento de canção maior que o comprimento médio de canção.
```sql
SELECT tra.Name,
    tra.Milliseconds
FROM Track tra
GROUP BY tra.Name
HAVING tra.Milliseconds > (SELECT AVG(Milliseconds) FROM Track)
ORDER BY tra.Milliseconds DESC
LIMIT 10;
```

Result:

| Name                          | Milliseconds |
| :---                          | ---:        |
| "Occupation / Precipice"      | "5286953"    |
| "Through a Looking Glass"     | "5088838"    |
| "Greetings from Earth, Pt. 1" | "2960293"    |
| "The Man With Nine Lives"     | "2956998"    |
| "Battlestar Galactica, Pt. 2" | "2956081"    |
| "Battlestar Galactica, Pt. 1" | "2952702"    |
| "Murder On the Rising Star"   | "2935894"    |
| "Battlestar Galactica, Pt. 3" | "2927802"    |
| "Take the Celestra"           | "2927677"    |
| "Fire In Space"               | "2926593"    |

```sql
-- 10 rows returned
```

## 3.3. Qual cliente gastou mais em músicas por país.

Query:

```sql
SELECT 
    cus_total.CustomerId,
    cus_total.Country,
    cus_total.FirstName,
    cus_total.LastName,
    MAX(cus_total.Total) TotalSpent
FROM 
	
    (SELECT 
        cus.CustomerId,
        cus.Country,
        cus.FirstName,
        cus.LastName,
        SUM(inv.Total) Total
    FROM Customer cus
    JOIN Invoice inv ON inv.CustomerId = cus.CustomerId
    GROUP BY 1, 2, 3, 4) cus_total

GROUP BY cus_total.Country, cus_total.FirstName
ORDER BY cus_total.Total DESC
LIMIT 10;
```

Result: 

| CustomerId | Contry           | FirstName  | LastName     | TotatSpent |
| :---       | :---             | :---       | :---         | ---:       |
| "6"        | "Czech Republic" | "Helena"   | "Holý"       | "49.62"    |
| "26"       | "USA"            | "Richard"  | "Cunningham" | "47.62"    |
| "57"       | "Chile"          | "Luis"     | "Rojas"      | "46.62"    |
| "45"       | "Hungary"        | "Ladislav" | "Kovács"     | "45.62"    |
| "46"       | "Ireland"        | "Hugh"     | "O'Reilly"   | "45.62"    |
| "28"       | "USA"            | "Julia"    | "Barnett"    | "43.62"    |
| "37"       | "Germany"        | "Fynn"     | "Zimmermann" | "43.62"    |
| "24"       | "USA"            | "Frank"    | "Ralston"    | "43.62"    |
| "7"        | "Austria"        | "Astrid"   | "Gruber"     | "42.62"    |
| "25"       | "USA"            | "Victor"   | "Stevens"    | "42.62"    |

```sql
-- 10 rows returned
```

## 4.1. Quais são os gêneros musicais mais vendidos?

Query:

```sql
SELECT
    gen.Name GenreName,
    COUNT(gen.Name) Quantity
FROM Genre gen
JOIN Track tra ON tra.GenreId = gen.GenreId
JOIN InvoiceLine invLine ON invLine.TrackId = tra.TrackId
GROUP BY GenreName
ORDER BY Quantity DESC
LIMIT 10;
```
Result: 

| GenreName            | Quantity |
| :---                 | ---:     |
| "Rock"               | "835"    |
| "Latin"              | "386"    |
| "Metal"              | "264"    |
| "Alternative & Punk" | "244"    |
| "Jazz"               | "80"     |
| "Blues"              | "61"     |
| "TV Shows"           | "47"     |
| "Classical"          | "41"     |
| "R&B/Soul"           | "41"     |
| "Reggae"             | "30"     |

```sql
-- 10 rows returned
```

## 4.2. Qual é o volume de vendas do gênero Rock por país?

Query:

```sql
SELECT 
    inv.BillingCountry Country,
    SUM(invLine.Quantity) Quantity
FROM Invoice inv
JOIN InvoiceLine invLine ON invLine.InvoiceId = inv.InvoiceId
JOIN Track tra ON tra.TrackId = invLine.TrackId
JOIN Genre gen ON gen.GenreId = tra.GenreId AND gen.Name = 'Rock'
GROUP BY Country
ORDER BY Quantity DESC;
```

Result: 

| Country          | Quantity |
| :---             | ---:     |
| "USA"            | "157"    |
| "Canada"         | "107"    |
| "Brazil"         | "81"     |
| "France"         | "65"     |
| "Germany"        | "62"     |
| "United Kingdom" | "37"     |
| "Portugal"       | "31"     |
| "Czech Republic" | "25"     |
| "India"          | "25"     |
| "Australia"      | "22"     |
| "Poland"         | "22"     |
| "Spain"          | "22"     |
| "Belgium"        | "21"     |
| "Denmark"        | "21"     |
| "Finland"        | "18"     |
| "Italy"          | "18"     |
| "Netherlands"    | "18"     |
| "Norway"         | "17"     |
| "Austria"        | "15"     |
| "Ireland"        | "12"     |
| "Hungary"        | "11"     |
| "Sweden"         | "10"     |
| "Argentina"      | "9"      |
| "Chile"          | "9"      |


```sql
-- 24 rows returned
```

## 4.3. Média de vendas do gênero musical Rock, por trimestre.

Query:

```sql
SELECT 
	invQuarter.Quarter,
    AVG(invQuarter.Quantity) avgQuantity
FROM 

    (SELECT
        inv.InvoiceId,
        inv.BillingCountry Country,
        inv.Total,
        SUM(invLine.Quantity) Quantity,
        strftime('%Y', inv.InvoiceDate) Year,
        CASE 
            WHEN strftime('%m', inv.InvoiceDate) IN ('01', '02', '03') THEN '1st Quarter'
            WHEN strftime('%m', inv.InvoiceDate) IN ('04', '05', '06') THEN '2nd Quarter'
            WHEN strftime('%m', inv.InvoiceDate) IN ('07', '08', '09') THEN '3rd Quarter'
            ELSE '4th Quarter'
        END AS Quarter

    FROM Invoice inv
    JOIN InvoiceLine invLine ON invLine.InvoiceId = inv.InvoiceId
	JOIN Track tra ON tra.TrackId = invLine.TrackId
	JOIN Genre gen ON gen.GenreId = tra.GenreId AND gen.Name = 'Rock'

    GROUP BY inv.InvoiceId, Country
    ORDER BY Country, Quarter) invQuarter

GROUP BY Quarter
```

Result:

| Quarter       | Avg Quantity       |
| :---          | ---:               |
| "1st Quarter" | "4.29787234042553" |
| "2nd Quarter" | "3.41666666666667" |
| "3rd Quarter" | "3.76271186440678" |
| "4th Quarter" | "4.12"             |

```sql
-- 4 rows returned
```