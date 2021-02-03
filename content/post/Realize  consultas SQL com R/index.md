---
aliases:
- migrate-from-jekyl
author: Clayton Silva
categories:
- SQL
- R
date: "2021-01-01"
description: Facilite seus trabalhos de análise em database com o RStudio.
image: https://images.pexels.com/photos/1183434/pexels-photo-1183434.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940
series:
- Themes Guide
#tags:
#- markdown
#- css
#- html
#- themes
title: Consultas SQL Com ODBC & Rstudio
---

Fala analista, tudo bem? Hoje vamos aprender a realizar consultas SQL diretamente pelo R atráves do Rstudio, utilizando o pacote odbc. 

Iremos utilizar o SGBD SQL server express 2017 instalado localmente, a base de dados é a AdventureWorks 2017 disponibilizada pela microsoft para que possamos brincar um pouco. Fique à vontade para utilizar com um banco de dados que você já possui. 

Estando devidamente configurado e hospedada sua base de dados é hora de partir para o Rstudio.

Vamos começar instalando e importando o pacote  ODBC. O ODBC permite acesso a uma série de SGBD utilizando a interface de linguagens de programação. 

```{r cars}
library(odbc)

```

Se você não tem esse pocote instalado, é só adicioná-lo com o comando abaixo.  `install.packages("odbc")`.

O próximo comando retorna todos os drives disponíveis que estão instalados em sua máquina. Importante que esteja listado `SQL Server`, utilizaremos ele para conectar ao SQL server.

```{r}
sort(unique(odbcListDrivers()[[1]]))
```

O próximo passo é realizar a conexão ao nosso banco de dados, informando o Drive, Server e o Database.

```{r}
con <- dbConnect(odbc(), 
                Driver = "SQL Server", 
                Server = "localhost\\SQLEXPRESS", 
                Database = "AdventureWorks2017", 
                Trusted_Connection = "True")
```

Prontinho! Agora estamos conectado ao SQL Server e ao nosso banco de dados. `AdventureWorks2017`.

Vamos realizar nossa primeira consulta em nosso banco de dados, e armazenar na variável `Person`. Na sequência iremos imprimir os dados armazenados vindo da nosssa consulta ao banco.

```{r}
person = dbGetQuery(con, "SELECT BusinessEntityID, FirstName, LastName FROM Person.Person")
head(person, n = 20)

```
Percebeu que eu selecionei os nomes das colunas ao realizar á consulta, isso por que eu já conhecia a tabela do *Schema Person*. No entanto, quando buscamos dados em uma tabela em que não sabemos os títulos da coluna passsamos a selecionar todos: `SELECT * FROM nomeTabela`. Esse comando aqui no R não funcionaria, devido a uma limitação de dados no ODBC, e isso é exttremamente limitante. 


Ainda assim é possível saber os `name` e `type` utilizando a função `odbcListColumns`.

```{r}
odbcListColumns(con, catalog="AdventureWorks2017", schema="Person", table="Person")
```

Há senários que o analista pode não saber os bancos e tabelas disponíveis no sistema, nesse caso a função `odbcListObjects()` o ajuda a identificar. 

Verificar os databases disponíveis: 
```{r}
odbcListObjects(con)
```

Buscando schemas na base de dados:

```{r, include=True}
odbcListObjects(con, catalog="AdventureWorks2017", schema="Person")
```

Se as informações passada aqui te ajudou, compartilha. Forte abraços e até a próxima. 


Referências: https://db.rstudio.com/odbc/