---
title: Trabalhar com dados JSON
description: Azure SQL Database e Azure SQL Managed Instance permitem-lhe analisar, consultar e formar dados na notação de objetos JavaScript (JSON).
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 53428a542bb8d8d546e68f63aaf80ee40f2b0874
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450239"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Começar com funcionalidades JSON na Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database e Azure SQL Managed Instance permitem-lhe analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON. Estão disponíveis os seguintes cenários JSON:

- [Formatar dados relacionais no formato JSON](#formatting-relational-data-in-json-format) usando `FOR JSON` a cláusula.
- [Trabalhar com dados JSON](#working-with-json-data)
- [Consultando os dados do JSON](#querying-json-data) utilizando funções de escala json.
- [Transformar o JSON em formato tabular](#transforming-json-into-tabular-format) utilizando `OPENJSON` a função.

## <a name="formatting-relational-data-in-json-format"></a>Formatação de dados relacionais no formato JSON

Se tiver um serviço web que retira dados da camada de base de dados e fornece uma resposta em formato JSON, ou quadros ou bibliotecas JavaScript do lado do cliente que aceitam dados formatados como JSON, pode formatar o seu conteúdo de base de dados como JSON diretamente numa consulta SQL. Já não tem de escrever código de aplicação que resulta da Base de Dados Azure SQL ou da Azure SQL Managed Instance como JSON, ou incluir alguma biblioteca de serialização JSON para converter resultados de consulta tabular e, em seguida, serializar objetos para o formato JSON. Em vez disso, pode utilizar a cláusula FOR JSON para formatar os resultados da consulta SQL como JSON e usá-lo diretamente na sua aplicação.

No exemplo seguinte, as linhas da tabela Sales.Cliente são formatadas como JSON utilizando a cláusula FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula FOR JSON PATH forma os resultados da consulta como texto JSON. Os nomes das colunas são usados como teclas, enquanto os valores celulares são gerados como valores JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON onde cada linha é formatada como um objeto JSON separado.

O PATH indica que pode personalizar o formato de saída do resultado do JSON utilizando a notação de pontos em pseudónimos de coluna. A seguinte consulta altera o nome da tecla "Nome do Cliente" no formato JSON de saída e coloca os números de telefone e de fax no sub-objecto "Contacto":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

A saída desta consulta é assim:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Neste exemplo, devolvemos um único objeto JSON em vez de uma matriz especificando a opção [WITHOUT_ARRAY_WRAPPER.](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) Pode utilizar esta opção se souber que está a devolver um único objeto como resultado de uma consulta.

O principal valor da cláusula FOR JSON é que permite devolver dados hierárquicos complexos da sua base de dados formatados como objetos ou matrizes JSON aninhados. O exemplo a seguir mostra como incluir as linhas da `Orders` tabela que pertencem à matriz `Customer` aninhada `Orders` de:

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Em vez de enviar consultas separadas para obter dados do Cliente e, em seguida, para obter uma lista de Encomendas relacionadas, você pode obter todos os dados necessários com uma única consulta, como mostrado na seguinte saída da amostra:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Trabalhar com dados JSON

Se não tiver dados estritamente estruturados, se tiver sub-objetos complexos, matrizes ou dados hierárquicos, ou se as suas estruturas de dados evoluírem ao longo do tempo, o formato JSON pode ajudá-lo a representar qualquer estrutura de dados complexa.

JSON é um formato textual que pode ser usado como qualquer outro tipo de corda na Base de Dados Azure SQL e Azure SQL Managed Instance. Pode enviar ou armazenar dados JSON como um NVARCHAR padrão:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Os dados JSON utilizados neste exemplo são representados através da utilização do tipo NVARCHAR(MAX). O JSON pode ser inserido nesta tabela ou apresentado como argumento do procedimento armazenado utilizando a sintaxe Transact-SQL padrão, como mostra o seguinte exemplo:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer idioma ou biblioteca do lado do cliente que funcione com dados de cadeia na Base de Dados Azure SQL e na Azure SQL Managed Instance também funcionará com dados JSON. O JSON pode ser armazenado em qualquer tabela que suporte o tipo NVARCHAR, como uma tabela otimizada pela memória ou uma tabela com versão do Sistema. A JSON não introduz qualquer restrição nem no código do lado do cliente nem na camada de base de dados.

## <a name="querying-json-data"></a>Consulta de dados JSON

Se tiver dados formatados como JSON armazenados em tabelas Azure SQL, as funções JSON permitem-lhe utilizar estes dados em qualquer consulta SQL.

As funções JSON que estão disponíveis na Base de Dados Azure SQL e na Azure SQL Managed Instance permitem tratar dados formatados como JSON como qualquer outro tipo de dados SQL. Pode extrair facilmente valores do texto JSON e utilizar dados JSON em qualquer consulta:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor do texto JSON armazenado na coluna Data. Esta função utiliza um caminho semelhante ao JavaScript para fazer referência a um valor no texto JSON para extrair. O valor extraído pode ser utilizado em qualquer parte da consulta SQL.

A função JSON_QUERY é semelhante a JSON_VALUE. Ao contrário JSON_VALUE, esta função extrai sub-objeto complexo, como matrizes ou objetos que são colocados no texto JSON.

A função JSON_MODIFY permite especificar o caminho do valor no texto JSON que deve ser atualizado, bem como um novo valor que substituirá o antigo. Desta forma, pode atualizar facilmente o texto JSON sem reparar toda a estrutura.

Uma vez que o JSON é armazenado num texto padrão, não existem garantias de que os valores armazenados em colunas de texto estejam devidamente formatados. Pode verificar se o texto armazenado na coluna JSON é devidamente formatado utilizando as restrições padrão de verificação da base de dados Azure SQL e a função ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada estiver devidamente formatado JSON, a função ISJSON devolve o valor 1. Em cada inserção ou atualização da coluna JSON, esta restrição verificará se o novo valor de texto não está mal formado JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformando json em formato tabular

A Azure SQL Database e Azure SQL Managed Instance também permitem transformar coleções JSON em formato tabular e carregar ou consultar dados JSON.

OPENJSON é uma função de valor de mesa que analisa o texto JSON, localiza uma matriz de objetos JSON, itera através dos elementos da matriz, e retorna uma linha no resultado de saída para cada elemento da matriz.

![JSON tabular](./media/json-features/image_2.png)

No exemplo acima, podemos especificar onde localizar a matriz JSON que deve ser aberta (no $. Percurso de encomendas), quais as colunas que devem ser devolvidas como resultado, e onde encontrar os valores JSON que serão devolvidos como células.

Podemos transformar uma matriz JSON na @orders variável em um conjunto de linhas, analisar este conjunto de resultados, ou inserir linhas em uma tabela padrão:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

A recolha de encomendas formatada como matriz JSON e fornecida como parâmetro para o procedimento armazenado pode ser analisada e inserida na tabela Encomendas.

## <a name="next-steps"></a>Passos seguintes

Para aprender a integrar a JSON na sua aplicação, confira estes recursos:

Para conhecer vários cenários para integrar o JSON na sua aplicação, consulte as demos neste [vídeo do Canal 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontre um cenário que corresponda ao seu caso de utilização em [publicações do JSON Blog](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases).


