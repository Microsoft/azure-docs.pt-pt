---
title: Trabalhar com dados JSON
description: A Base de Dados Azure SQL e a Instância Gerida Azure SQL permitem-lhe analisar, consultar e formato dados na notação javaScript Object Notation (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: c94aac5d90611f6fca2d697d23d0c8d1bcf6a359
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048435"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Começar com funcionalidades JSON em Base de Dados Azure SQL e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Base de Dados Azure SQL e a Instância Gerida Azure SQL permitem-lhe analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON. Estão disponíveis os seguintes cenários jSON:

- [Formatação de dados relacionais em formato JSON](#formatting-relational-data-in-json-format) utilizando `FOR JSON` cláusula.
- [Trabalhar com dados JSON](#working-with-json-data)
- [Consulta de dados JSON](#querying-json-data) utilizando funções de escalar JSON.
- Transformar a [JSON em formato tabular](#transforming-json-into-tabular-format) utilizando `OPENJSON` a função.

## <a name="formatting-relational-data-in-json-format"></a>Formatação de dados relacionais em formato JSON

Se tiver um serviço web que retire dados da camada de base de dados e forneça uma resposta no formato JSON, ou quadros javaScript ou bibliotecas do lado do cliente que aceitam dados formados como JSON, pode formatar o seu conteúdo de base de dados como JSON diretamente numa consulta SQL. Já não tem de escrever o código de aplicação que forma tome resultados da Base de Dados Azure SQL ou do Azure SQL Managed Instance como JSON, ou incluir alguma biblioteca de serialização JSON para converter resultados de consulta tabular e depois serializar objetos para o formato JSON. Em vez disso, pode utilizar a cláusula FOR JSON para formatar os resultados da consulta SQL como JSON e usá-lo diretamente na sua aplicação.

No exemplo seguinte, as filas da tabela Sales.Customer são formatadas como JSON utilizando a cláusula FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula FOR JSON PATH formata os resultados da consulta como texto JSON. Os nomes das colunas são usados como teclas, enquanto os valores celulares são gerados como valores JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON onde cada linha é formatada como um objeto JSON separado.

O PATH indica que pode personalizar o formato de saída do seu resultado JSON utilizando notação de pontos em pseudónimos de coluna. A seguinte consulta altera o nome da tecla "Nome do Cliente" no formato JSON de saída e coloca os números de telefone e de fax no sub-objecto "Contacto":

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

Neste exemplo, devolvemos um único objeto JSON em vez de uma matriz especificando a [opção WITHOUT_ARRAY_WRAPPER.](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) Pode utilizar esta opção se souber que está a devolver um único objeto como resultado de consulta.

O principal valor da cláusula FOR JSON é que permite devolver dados hierárquicos complexos da sua base de dados formatados como objetos ou matrizes JSON aninhados. O exemplo que se segue mostra como incluir as linhas da `Orders` mesa que pertencem à seguinte gama `Customer` `Orders` de:

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Em vez de enviar consultas separadas para obter dados do Cliente e, em seguida, para obter uma lista de Encomendas relacionadas, você pode obter todos os dados necessários com uma única consulta, como mostrado na seguinte saída de amostra:

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

Se não tiver dados estritamente estruturados, se tiver subobjetos complexos, matrizes ou dados hierárquicos, ou se as suas estruturas de dados evoluirem ao longo do tempo, o formato JSON pode ajudá-lo a representar qualquer estrutura de dados complexa.

JSON é um formato textual que pode ser usado como qualquer outro tipo de corda em Azure SQL Database e Azure SQL Managed Instance. Pode enviar ou armazenar dados da JSON como um NVARCHAR padrão:

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

Os dados jSON utilizados neste exemplo são representados utilizando o tipo NVARCHAR(MAX). A JSON pode ser inserida nesta tabela ou fornecida como argumento do procedimento armazenado utilizando a sintaxe padrão Transact-SQL, como mostra o seguinte exemplo:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer idioma ou biblioteca do lado do cliente que trabalhe com dados de cordas na Base de Dados Azure SQL e na Instância Gerida Azure SQL também funcionará com dados da JSON. A JSON pode ser armazenada em qualquer tabela que suporte o tipo NVARCHAR, como uma tabela otimizada pela Memória ou uma tabela versífica do Sistema. A JSON não introduz qualquer restrição nem no código do lado do cliente nem na camada de base de dados.

## <a name="querying-json-data"></a>Consulta de dados jSON

Se tiver dados formatados como JSON armazenados em tabelas Azure SQL, as funções JSON permitem-lhe utilizar estes dados em qualquer consulta SQL.

As funções JSON que estão disponíveis na Base de Dados Azure SQL e na Instância Gerida Azure SQL permitem tratar os dados formatados como JSON como qualquer outro tipo de dados SQL. Pode extrair facilmente valores do texto JSON e utilizar dados JSON em qualquer consulta:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor do texto JSON armazenado na coluna Dados. Esta função utiliza um caminho semelhante ao JavaScript para referir um valor no texto JSON para extrair. O valor extraído pode ser utilizado em qualquer parte da consulta SQL.

A função JSON_QUERY é semelhante à JSON_VALUE. Ao contrário JSON_VALUE, esta função extrai sub-objectos complexos, tais como matrizes ou objetos que são colocados em texto JSON.

A função JSON_MODIFY permite especificar o caminho do valor no texto JSON que deve ser atualizado, bem como um novo valor que irá sobrepor o antigo. Desta forma, pode atualizar facilmente o texto JSON sem reparparparsar toda a estrutura.

Uma vez que a JSON é armazenada num texto padrão, não existem garantias de que os valores armazenados nas colunas de texto estejam devidamente formatados. Pode verificar se o texto armazenado na coluna JSON está devidamente formatado utilizando restrições padrão de verificação da Base de Dados Azure SQL e da função ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada for devidamente formatado JSON, a função ISJSON devolve o valor 1. Em cada inserção ou atualização da coluna JSON, esta restrição verificará se o novo valor de texto não está mal formado jSON.

## <a name="transforming-json-into-tabular-format"></a>Transformar a JSON em formato tabular

A Base de Dados Azure SQL e a Instância Gerida Azure SQL também permitem transformar as coleções JSON em formato tabular e carregar ou consultar dados da JSON.

OPENJSON é uma função de valor de tabela que analisa o texto JSON, localiza uma matriz de objetos JSON, iterates através dos elementos da matriz, e devolve uma linha no resultado de saída para cada elemento da matriz.

![Tabular JSON](./media/json-features/image_2.png)

No exemplo acima, podemos especificar onde localizar a matriz JSON que deve ser aberta (nos $. Rota das encomendas), que colunas devem ser devolvidas como resultado, e onde encontrar os valores JSON que serão devolvidos como células.

Podemos transformar uma matriz JSON na @orders variável num conjunto de linhas, analisar este conjunto de resultados ou inserir linhas numa tabela padrão:

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

A recolha de encomendas formatadas como matriz JSON e fornecida como parâmetro ao procedimento armazenado pode ser analisada e inserida na tabela Encomendas.

## <a name="next-steps"></a>Próximos passos

Para aprender a integrar a JSON na sua aplicação, confira estes recursos:

Para conhecer vários cenários para integrar a JSON na sua aplicação, consulte as demonstrações neste [vídeo do Canal 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontre um cenário que corresponda ao seu caso de utilização em [posts do JSON Blog.](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases)


