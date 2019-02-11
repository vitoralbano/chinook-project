# Projeto Chinook
# Perguntas:
1. Conjunto de perguntas 01
    1. [Quais países possuem mais faturas?](#11)
    2. [Qual cidade tem os melhores clientes?](#12-Qual-cidade-tem-os-melhores-clientes)
    3. [Quem é o melhor cliente?](#13-Quem-é-o-melhor-cliente?)
2. Conjunto de perguntas 02
    1. [Use sua consulta para retornar o e-mail, nome, sobrenome e gênero de todos os ouvintes de Rock. Retorne sua lista ordenada alfabeticamente por endereço de e-mail, começando por A.](#21-Use-sua-consulta-para-retornar-o-e-mail,-nome,-sobrenome-e-gênero-de-todos-os-ouvintes-de-Rock.-Retorne-sua-lista-ordenada-alfabeticamente-por-endereço-de-e-mail,-começando-por-A)
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
    2. [Qual é o volume de vendas por trimestre por ano?](#42-Qual-é-o-volume-de-vendas-por-trimestre-por-ano?)

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

## 2.1. Use sua consulta para retornar o e-mail, nome, sobrenome e gênero de todos os ouvintes de Rock. Retorne sua lista ordenada alfabeticamente por endereço de e-mail, começando por A.
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

## 2.3.2. Agora encontre qual cliente gastou mais com o artista que você encontrou acima
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

## 3.3 Qual cliente gastou mais em músicas por país.

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

## 4.2. Qual é o volume de vendas por trimestre por ano?

Query:

```sql
SELECT 
    invQuarter.Country,
    SUM(invQuarter.Quantity) Quantity,
    SUM(invQuarter.Total) Total,
    printf('%s %s', invQuarter.Year, invQuarter.Quarter) Period
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

    GROUP BY inv.InvoiceId, Country
    ORDER BY Country, Quarter) invQuarter

GROUP BY Country, Year, Quarter
```

Result:

| Country          | Quantity | Total   | Period             |
| :---             | ---:     | ---:    | :---               |
| "Argentina"      | "2"      | "1.98"  | "2010 2nd Quarter" |
| "Argentina"      | "4"      | "3.96"  | "2010 3rd Quarter" |
| "Argentina"      | "6"      | "5.94"  | "2010 4th Quarter" |
| "Argentina"      | "1"      | "0.99"  | "2011 3rd Quarter" |
| "Argentina"      | "16"     | "15.84" | "2013 1st Quarter" |
| "Argentina"      | "9"      | "8.91"  | "2013 4th Quarter" |
| "Australia"      | "2"      | "1.98"  | "2009 2nd Quarter" |
| "Australia"      | "4"      | "3.96"  | "2009 3rd Quarter" |
| "Australia"      | "6"      | "5.94"  | "2009 4th Quarter" |
| "Australia"      | "1"      | "0.99"  | "2010 2nd Quarter" |
| "Australia"      | "2"      | "1.98"  | "2011 4th Quarter" |
| "Australia"      | "14"     | "13.86" | "2012 1st Quarter" |
| "Australia"      | "9"      | "8.91"  | "2012 3rd Quarter" |
| "Austria"        | "2"      | "1.98"  | "2009 4th Quarter" |
| "Austria"        | "14"     | "18.86" | "2010 1st Quarter" |
| "Austria"        | "9"      | "8.91"  | "2010 3rd Quarter" |
| "Austria"        | "2"      | "1.98"  | "2012 2nd Quarter" |
| "Austria"        | "4"      | "3.96"  | "2012 3rd Quarter" |
| "Austria"        | "6"      | "5.94"  | "2012 4th Quarter" |
| "Austria"        | "1"      | "0.99"  | "2013 2nd Quarter" |
| "Belgium"        | "6"      | "5.94"  | "2009 1st Quarter" |
| "Belgium"        | "1"      | "0.99"  | "2009 3rd Quarter" |
| "Belgium"        | "16"     | "15.84" | "2011 1st Quarter" |
| "Belgium"        | "9"      | "8.91"  | "2011 4th Quarter" |
| "Belgium"        | "2"      | "1.98"  | "2013 3rd Quarter" |
| "Belgium"        | "4"      | "3.96"  | "2013 4th Quarter" |
| "Brazil"         | "12"     | "11.88" | "2009 2nd Quarter" |
| "Brazil"         | "6"      | "5.94"  | "2009 3rd Quarter" |
| "Brazil"         | "20"     | "19.8"  | "2009 4th Quarter" |
| "Brazil"         | "2"      | "3.98"  | "2010 1st Quarter" |
| "Brazil"         | "13"     | "12.87" | "2010 2nd Quarter" |
| "Brazil"         | "7"      | "6.93"  | "2010 3rd Quarter" |
| "Brazil"         | "18"     | "17.82" | "2010 4th Quarter" |
| "Brazil"         | "4"      | "3.96"  | "2011 1st Quarter" |
| "Brazil"         | "7"      | "6.93"  | "2011 2nd Quarter" |
| "Brazil"         | "9"      | "8.91"  | "2011 3rd Quarter" |
| "Brazil"         | "19"     | "18.81" | "2012 1st Quarter" |
| "Brazil"         | "4"      | "3.96"  | "2012 2nd Quarter" |
| "Brazil"         | "6"      | "5.94"  | "2012 3rd Quarter" |
| "Brazil"         | "25"     | "24.75" | "2012 4th Quarter" |
| "Brazil"         | "3"      | "2.97"  | "2013 1st Quarter" |
| "Brazil"         | "29"     | "28.71" | "2013 3rd Quarter" |
| "Brazil"         | "6"      | "5.94"  | "2013 4th Quarter" |
| "Canada"         | "18"     | "17.82" | "2009 1st Quarter" |
| "Canada"         | "3"      | "2.97"  | "2009 2nd Quarter" |
| "Canada"         | "33"     | "32.67" | "2009 3rd Quarter" |
| "Canada"         | "4"      | "3.96"  | "2009 4th Quarter" |
| "Canada"         | "17"     | "19.83" | "2010 1st Quarter" |
| "Canada"         | "23"     | "22.77" | "2010 2nd Quarter" |
| "Canada"         | "2"      | "1.98"  | "2010 3rd Quarter" |
| "Canada"         | "32"     | "31.68" | "2010 4th Quarter" |
| "Canada"         | "26"     | "25.74" | "2011 1st Quarter" |
| "Canada"         | "6"      | "5.94"  | "2011 2nd Quarter" |
| "Canada"         | "9"      | "8.91"  | "2011 3rd Quarter" |
| "Canada"         | "15"     | "14.85" | "2011 4th Quarter" |
| "Canada"         | "10"     | "9.9"   | "2012 1st Quarter" |
| "Canada"         | "26"     | "25.74" | "2012 2nd Quarter" |
| "Canada"         | "2"      | "1.98"  | "2012 3rd Quarter" |
| "Canada"         | "5"      | "4.95"  | "2012 4th Quarter" |
| "Canada"         | "20"     | "19.8"  | "2013 1st Quarter" |
| "Canada"         | "22"     | "21.78" | "2013 2nd Quarter" |
| "Canada"         | "25"     | "24.75" | "2013 3rd Quarter" |
| "Canada"         | "6"      | "5.94"  | "2013 4th Quarter" |
| "Chile"          | "16"     | "15.84" | "2009 2nd Quarter" |
| "Chile"          | "9"      | "17.91" | "2010 1st Quarter" |
| "Chile"          | "2"      | "1.98"  | "2011 3rd Quarter" |
| "Chile"          | "4"      | "3.96"  | "2011 4th Quarter" |
| "Chile"          | "6"      | "5.94"  | "2012 1st Quarter" |
| "Chile"          | "1"      | "0.99"  | "2012 4th Quarter" |
| "Czech Republic" | "9"      | "8.91"  | "2009 3rd Quarter" |
| "Czech Republic" | "2"      | "1.98"  | "2009 4th Quarter" |
| "Czech Republic" | "4"      | "3.96"  | "2010 1st Quarter" |
| "Czech Republic" | "6"      | "5.94"  | "2010 2nd Quarter" |
| "Czech Republic" | "3"      | "2.97"  | "2011 1st Quarter" |
| "Czech Republic" | "4"      | "3.96"  | "2011 2nd Quarter" |
| "Czech Republic" | "6"      | "5.94"  | "2011 3rd Quarter" |
| "Czech Republic" | "1"      | "0.99"  | "2012 2nd Quarter" |
| "Czech Republic" | "16"     | "18.84" | "2012 3rd Quarter" |
| "Czech Republic" | "9"      | "8.91"  | "2013 2nd Quarter" |
| "Czech Republic" | "16"     | "27.84" | "2013 4th Quarter" |
| "Denmark"        | "2"      | "1.98"  | "2009 3rd Quarter" |
| "Denmark"        | "4"      | "3.96"  | "2009 4th Quarter" |
| "Denmark"        | "6"      | "5.94"  | "2010 1st Quarter" |
| "Denmark"        | "1"      | "0.99"  | "2010 4th Quarter" |
| "Denmark"        | "16"     | "15.84" | "2012 2nd Quarter" |
| "Denmark"        | "9"      | "8.91"  | "2013 1st Quarter" |
| "Finland"        | "9"      | "8.91"  | "2009 3rd Quarter" |
| "Finland"        | "2"      | "1.98"  | "2011 1st Quarter" |
| "Finland"        | "4"      | "7.96"  | "2011 2nd Quarter" |
| "Finland"        | "6"      | "5.94"  | "2011 3rd Quarter" |
| "Finland"        | "1"      | "0.99"  | "2012 2nd Quarter" |
| "Finland"        | "16"     | "15.84" | "2013 4th Quarter" |
| "France"         | "20"     | "19.8"  | "2009 1st Quarter" |
| "France"         | "6"      | "5.94"  | "2009 2nd Quarter" |
| "France"         | "10"     | "9.9"   | "2009 4th Quarter" |
| "France"         | "2"      | "1.98"  | "2010 1st Quarter" |
| "France"         | "22"     | "21.78" | "2010 2nd Quarter" |
| "France"         | "10"     | "9.9"   | "2010 3rd Quarter" |
| "France"         | "6"      | "5.94"  | "2010 4th Quarter" |
| "France"         | "10"     | "9.9"   | "2011 1st Quarter" |
| "France"         | "5"      | "8.95"  | "2011 2nd Quarter" |
| "France"         | "18"     | "17.82" | "2011 3rd Quarter" |
| "France"         | "6"      | "5.94"  | "2011 4th Quarter" |
| "France"         | "9"      | "8.91"  | "2012 1st Quarter" |
| "France"         | "5"      | "4.95"  | "2012 3rd Quarter" |
| "France"         | "20"     | "22.8"  | "2012 4th Quarter" |
| "France"         | "20"     | "19.8"  | "2013 1st Quarter" |
| "France"         | "9"      | "8.91"  | "2013 2nd Quarter" |
| "France"         | "9"      | "8.91"  | "2013 3rd Quarter" |
| "France"         | "3"      | "2.97"  | "2013 4th Quarter" |
| "Germany"        | "19"     | "18.81" | "2009 1st Quarter" |
| "Germany"        | "20"     | "19.8"  | "2009 2nd Quarter" |
| "Germany"        | "6"      | "5.94"  | "2009 3rd Quarter" |
| "Germany"        | "9"      | "8.91"  | "2009 4th Quarter" |
| "Germany"        | "10"     | "9.9"   | "2010 1st Quarter" |
| "Germany"        | "16"     | "15.84" | "2010 3rd Quarter" |
| "Germany"        | "11"     | "16.89" | "2011 2nd Quarter" |
| "Germany"        | "8"      | "7.92"  | "2011 3rd Quarter" |
| "Germany"        | "24"     | "23.76" | "2011 4th Quarter" |
| "Germany"        | "6"      | "5.94"  | "2012 1st Quarter" |
| "Germany"        | "9"      | "8.91"  | "2012 2nd Quarter" |
| "Germany"        | "1"      | "0.99"  | "2012 3rd Quarter" |
| "Germany"        | "3"      | "2.97"  | "2012 4th Quarter" |
| "Germany"        | "4"      | "3.96"  | "2013 1st Quarter" |
| "Germany"        | "6"      | "5.94"  | "2013 2nd Quarter" |
| "Hungary"        | "16"     | "23.84" | "2010 1st Quarter" |
| "Hungary"        | "9"      | "8.91"  | "2010 4th Quarter" |
| "Hungary"        | "2"      | "1.98"  | "2012 2nd Quarter" |
| "Hungary"        | "4"      | "3.96"  | "2012 3rd Quarter" |
| "Hungary"        | "6"      | "5.94"  | "2012 4th Quarter" |
| "Hungary"        | "1"      | "0.99"  | "2013 3rd Quarter" |
| "India"          | "4"      | "3.96"  | "2009 2nd Quarter" |
| "India"          | "6"      | "5.94"  | "2009 3rd Quarter" |
| "India"          | "1"      | "1.99"  | "2010 1st Quarter" |
| "India"          | "2"      | "1.98"  | "2010 2nd Quarter" |
| "India"          | "14"     | "13.86" | "2010 3rd Quarter" |
| "India"          | "9"      | "8.91"  | "2011 1st Quarter" |
| "India"          | "16"     | "15.84" | "2011 3rd Quarter" |
| "India"          | "9"      | "8.91"  | "2012 2nd Quarter" |
| "India"          | "2"      | "1.98"  | "2012 4th Quarter" |
| "India"          | "4"      | "3.96"  | "2013 1st Quarter" |
| "India"          | "6"      | "5.94"  | "2013 2nd Quarter" |
| "India"          | "1"      | "1.99"  | "2013 4th Quarter" |
| "Ireland"        | "6"      | "5.94"  | "2009 1st Quarter" |
| "Ireland"        | "1"      | "0.99"  | "2009 3rd Quarter" |
| "Ireland"        | "2"      | "1.98"  | "2011 1st Quarter" |
| "Ireland"        | "14"     | "21.86" | "2011 2nd Quarter" |
| "Ireland"        | "9"      | "8.91"  | "2011 4th Quarter" |
| "Ireland"        | "2"      | "1.98"  | "2013 3rd Quarter" |
| "Ireland"        | "4"      | "3.96"  | "2013 4th Quarter" |
| "Italy"          | "2"      | "1.98"  | "2009 4th Quarter" |
| "Italy"          | "4"      | "3.96"  | "2010 1st Quarter" |
| "Italy"          | "6"      | "5.94"  | "2010 2nd Quarter" |
| "Italy"          | "1"      | "0.99"  | "2010 4th Quarter" |
| "Italy"          | "2"      | "1.98"  | "2012 2nd Quarter" |
| "Italy"          | "14"     | "13.86" | "2012 3rd Quarter" |
| "Italy"          | "9"      | "8.91"  | "2013 1st Quarter" |
| "Netherlands"    | "9"      | "8.91"  | "2009 2nd Quarter" |
| "Netherlands"    | "2"      | "1.98"  | "2010 4th Quarter" |
| "Netherlands"    | "4"      | "3.96"  | "2011 1st Quarter" |
| "Netherlands"    | "6"      | "8.94"  | "2011 2nd Quarter" |
| "Netherlands"    | "1"      | "0.99"  | "2012 1st Quarter" |
| "Netherlands"    | "16"     | "15.84" | "2013 3rd Quarter" |
| "Norway"         | "4"      | "3.96"  | "2009 1st Quarter" |
| "Norway"         | "6"      | "5.94"  | "2009 2nd Quarter" |
| "Norway"         | "1"      | "0.99"  | "2009 4th Quarter" |
| "Norway"         | "16"     | "17.84" | "2011 2nd Quarter" |
| "Norway"         | "9"      | "8.91"  | "2012 1st Quarter" |
| "Norway"         | "2"      | "1.98"  | "2013 4th Quarter" |
| "Poland"         | "16"     | "15.84" | "2009 4th Quarter" |
| "Poland"         | "9"      | "8.91"  | "2010 3rd Quarter" |
| "Poland"         | "2"      | "1.98"  | "2012 1st Quarter" |
| "Poland"         | "4"      | "3.96"  | "2012 2nd Quarter" |
| "Poland"         | "6"      | "5.94"  | "2012 3rd Quarter" |
| "Poland"         | "1"      | "0.99"  | "2013 2nd Quarter" |
| "Portugal"       | "2"      | "1.98"  | "2009 2nd Quarter" |
| "Portugal"       | "4"      | "3.96"  | "2009 3rd Quarter" |
| "Portugal"       | "6"      | "5.94"  | "2009 4th Quarter" |
| "Portugal"       | "1"      | "0.99"  | "2010 2nd Quarter" |
| "Portugal"       | "2"      | "1.98"  | "2010 3rd Quarter" |
| "Portugal"       | "4"      | "3.96"  | "2010 4th Quarter" |
| "Portugal"       | "6"      | "5.94"  | "2011 1st Quarter" |
| "Portugal"       | "1"      | "0.99"  | "2011 3rd Quarter" |
| "Portugal"       | "2"      | "1.98"  | "2011 4th Quarter" |
| "Portugal"       | "14"     | "13.86" | "2012 1st Quarter" |
| "Portugal"       | "9"      | "10.91" | "2012 4th Quarter" |
| "Portugal"       | "2"      | "1.98"  | "2013 1st Quarter" |
| "Portugal"       | "14"     | "13.86" | "2013 2nd Quarter" |
| "Portugal"       | "9"      | "8.91"  | "2013 4th Quarter" |
| "Spain"          | "1"      | "0.99"  | "2009 2nd Quarter" |
| "Spain"          | "2"      | "1.98"  | "2010 4th Quarter" |
| "Spain"          | "14"     | "13.86" | "2011 1st Quarter" |
| "Spain"          | "9"      | "8.91"  | "2011 3rd Quarter" |
| "Spain"          | "2"      | "1.98"  | "2013 2nd Quarter" |
| "Spain"          | "4"      | "3.96"  | "2013 3rd Quarter" |
| "Spain"          | "6"      | "5.94"  | "2013 4th Quarter" |
| "Sweden"         | "2"      | "1.98"  | "2009 3rd Quarter" |
| "Sweden"         | "4"      | "3.96"  | "2009 4th Quarter" |
| "Sweden"         | "6"      | "6.94"  | "2010 1st Quarter" |
| "Sweden"         | "1"      | "0.99"  | "2010 3rd Quarter" |
| "Sweden"         | "2"      | "1.98"  | "2012 1st Quarter" |
| "Sweden"         | "14"     | "13.86" | "2012 2nd Quarter" |
| "Sweden"         | "9"      | "8.91"  | "2012 4th Quarter" |
| "USA"            | "29"     | "28.71" | "2009 1st Quarter" |
| "USA"            | "33"     | "32.67" | "2009 2nd Quarter" |
| "USA"            | "15"     | "14.85" | "2009 3rd Quarter" |
| "USA"            | "28"     | "27.72" | "2009 4th Quarter" |
| "USA"            | "23"     | "24.77" | "2010 1st Quarter" |
| "USA"            | "29"     | "28.71" | "2010 2nd Quarter" |
| "USA"            | "35"     | "34.65" | "2010 3rd Quarter" |
| "USA"            | "15"     | "14.85" | "2010 4th Quarter" |
| "USA"            | "12"     | "11.88" | "2011 1st Quarter" |
| "USA"            | "32"     | "36.68" | "2011 2nd Quarter" |
| "USA"            | "29"     | "28.71" | "2011 3rd Quarter" |
| "USA"            | "26"     | "25.74" | "2011 4th Quarter" |
| "USA"            | "18"     | "17.82" | "2012 1st Quarter" |
| "USA"            | "18"     | "17.82" | "2012 2nd Quarter" |
| "USA"            | "38"     | "64.62" | "2012 3rd Quarter" |
| "USA"            | "28"     | "27.72" | "2012 4th Quarter" |
| "USA"            | "14"     | "13.86" | "2013 1st Quarter" |
| "USA"            | "20"     | "19.8"  | "2013 2nd Quarter" |
| "USA"            | "20"     | "19.8"  | "2013 3rd Quarter" |
| "USA"            | "32"     | "31.68" | "2013 4th Quarter" |
| "United Kingdom" | "10"     | "9.9"   | "2009 1st Quarter" |
| "United Kingdom" | "16"     | "15.84" | "2009 3rd Quarter" |
| "United Kingdom" | "9"      | "8.91"  | "2010 2nd Quarter" |
| "United Kingdom" | "4"      | "3.96"  | "2010 3rd Quarter" |
| "United Kingdom" | "18"     | "17.82" | "2010 4th Quarter" |
| "United Kingdom" | "6"      | "5.94"  | "2011 1st Quarter" |
| "United Kingdom" | "9"      | "8.91"  | "2011 2nd Quarter" |
| "United Kingdom" | "3"      | "2.97"  | "2011 4th Quarter" |
| "United Kingdom" | "4"      | "3.96"  | "2012 1st Quarter" |
| "United Kingdom" | "6"      | "5.94"  | "2012 2nd Quarter" |
| "United Kingdom" | "3"      | "2.97"  | "2013 1st Quarter" |
| "United Kingdom" | "20"     | "19.8"  | "2013 2nd Quarter" |
| "United Kingdom" | "6"      | "5.94"  | "2013 3rd Quarter" |

```sql
-- 236 rows returned
```