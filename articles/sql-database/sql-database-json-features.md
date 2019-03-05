---
title: Trabalhar com dados JSON no Azure SQL Database | Documentos da Microsoft
description: Base de dados SQL do Azure permite-lhe analisar, consulta e formatar dados na notação de JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 02cec7f3696a43e8effe9d551c1f3e3fddc25ec8
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317208"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introdução com funcionalidades JSON na base de dados do Azure SQL
Azure base de dados SQL permite-lhe analisa e consulta dados representados no JavaScript Object Notation [(JSON)](http://www.json.org/) formate e exportar os seus dados relacionais como texto JSON. Os seguintes cenários JSON estão disponíveis na base de dados do Azure SQL:
- [Formatação de dados relacionais no formato JSON](#formatting-relational-data-in-json-format) usando `FOR JSON` cláusula.
- [Trabalhar com dados JSON](#working-with-json-data)
- [Consultar dados JSON](#querying-json-data) com as funções escalares de JSON.
- [Transformar JSON em formato tabular](#transforming-json-into-tabular-format) usando `OPENJSON` função.

## <a name="formatting-relational-data-in-json-format"></a>Formatação de dados relacionais no formato JSON
Se tiver um serviço da web que utiliza dados da base de dados de camada e fornece uma resposta em JSON formatar ou estruturas JavaScript do lado do cliente ou a bibliotecas que aceitam dados formatados como JSON, pode formatar o conteúdo de base de dados como JSON diretamente numa consulta SQL. Já não precisa escrever código do aplicativo que formata os resultados da base de dados do Azure SQL como JSON ou incluir alguma biblioteca de serialização do JSON para converter os resultados de consulta de tabela e, em seguida, serializar objetos no formato JSON. Em vez disso, pode usar a cláusula FOR JSON para formatar os resultados da consulta SQL como JSON na SQL Database do Azure e utilizá-lo diretamente na sua aplicação.

No exemplo seguinte, as linhas da tabela Sales. Customer são formatadas como JSON ao utilizar a cláusula FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula FOR JSON PATH formata os resultados da consulta como texto JSON. Os nomes das colunas são utilizadas como chaves, enquanto os valores de célula são gerados como valores JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON em que cada linha é formatada como um objeto JSON separado.

CAMINHO indica que pode personalizar o formato de saída do seu resultado JSON, utilizando a notação de ponto no aliases de coluna. A consulta seguinte altera o nome da chave "CustomerName" no formato JSON de saída e coloca os números de telefone e fax no objeto secundárias "Contact":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

A saída dessa consulta tem o seguinte aspeto:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Neste exemplo, é devolvido um único objeto JSON em vez de uma matriz, especificando o [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) opção. Pode utilizar esta opção se souber que está retornando um único objeto como resultado da consulta.

O valor principal da cláusula FOR JSON é o que permite-lhe devolver dados hierárquicos complexos da sua base de dados formatado como objetos aninhados de JSON ou matrizes. O exemplo seguinte mostra como incluir as linhas dos `Orders` tabela que pertencem ao `Customer` como uma matriz aninhada de `Orders`:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Em vez de enviar a separar as consultas para obter dados do cliente e, em seguida, para obter uma lista de pedidos relacionados, pode obter todos os dados necessários com uma única consulta, conforme mostrado no seguinte exemplo:

```
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
Se não tiver dados estruturados estritamente, se tiver subobjetos complexos, matrizes ou dados hierárquicos, ou se suas estruturas de dados evoluam ao longo do tempo, o formato JSON pode ajudá-lo a para representar qualquer estrutura de dados complexos.

JSON é um formato de texto que pode ser utilizado como qualquer outro tipo de cadeia de caracteres na base de dados do Azure SQL. Pode enviar ou armazenar dados JSON como um NVARCHAR padrão:

```
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

Os dados JSON utilizados neste exemplo são representados através do tipo nvarchar (Max). JSON pode ser inserido nessa tabela ou fornecido como um argumento do procedimento armazenado com a sintaxe do Transact-SQL padrão, conforme mostrado no exemplo seguinte:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer linguagem no lado do cliente ou a biblioteca que funciona com dados de cadeia de caracteres na SQL Database do Azure também irão funcionar com dados JSON. JSON pode ser armazenado em qualquer tabela que suporta o tipo NVARCHAR, como uma tabela com otimização de memória ou uma tabela com versão do sistema. JSON não introduz nenhuma restrição no código do lado do cliente ou na camada de base de dados.

## <a name="querying-json-data"></a>Consultar dados JSON
Se tiver dados formatados como JSON armazenado em tabelas de SQL do Azure, o funções JSON permitem-lhe utilizar estes dados de qualquer consulta SQL.

Funções JSON que estão disponíveis no let de base de dados SQL do Azure trata dados formatados como JSON como qualquer outro tipo de dados SQL. Pode extrair valores de texto JSON e utilizar facilmente dados JSON de qualquer consulta:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor de texto JSON armazenado na coluna de dados. Esta função utiliza um caminho de como o JavaScript para fazer referência a um valor no texto JSON para extrair. O valor extraído pode ser utilizado em qualquer parte da consulta SQL.

A função JSON_QUERY é semelhante à JSON_VALUE. Ao contrário de JSON_VALUE, esta função extrai subobjeto complexo, como matrizes ou objetos que são colocados no texto JSON.

A função JSON_MODIFY permite-lhe especificar o caminho do valor em texto JSON que deve ser atualizado, bem como um novo valor que substituirá o antigo. Desta forma, que pode atualizar facilmente texto JSON sem reparsing a estrutura inteira.

Uma vez que o JSON é armazenado num texto padrão, não há garantias que os valores armazenados nas colunas de texto estão formatados corretamente. Pode verificar que o texto armazenado na coluna do JSON está formatado corretamente com restrições de verificação padrão do banco de dados do Azure SQL e a função ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada está corretamente formado JSON, a função ISJSON devolve o valor 1. Em cada inserção ou atualização da coluna JSON, essa restrição irá verificar que o novo valor de texto não é um JSON com formato incorreto.

## <a name="transforming-json-into-tabular-format"></a>Transformar JSON em formato tabular
Base de dados SQL do Azure também permite transformar coleções de JSON para dados de JSON de formato e a carga ou a consulta de tabela.

OPENJSON é uma função de valor de tabela que analisa o texto JSON, localiza uma matriz de objetos JSON, itera através de elementos da matriz e retorna uma linha no resultado da saída para cada elemento da matriz.

![JSON tabular](./media/sql-database-json-features/image_2.png)

No exemplo acima, podemos especificar onde localizar a matriz JSON que deve ser aberta (em $. Caminho de ordens), que colunas devem ser devolvidas como resultado e onde encontrar os valores JSON que vão ser devolvidos como células.

Podemos pode transformar uma matriz JSON no @orders variável num conjunto de linhas, analisar este conjunto de resultados ou inserir linhas numa tabela padrão:

```
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
A coleção de encomendas formatados como uma matriz JSON e fornecido como um parâmetro para o procedimento armazenado pode ser analisado e inserido na tabela Orders.

## <a name="next-steps"></a>Passos Seguintes
Para saber como integrar o JSON na sua aplicação, veja estes recursos:

* [Blogue do TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [Documentação do MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Vídeo do Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Para saber mais sobre os vários cenários de integração do JSON à sua aplicação, veja as demonstrações desta [vídeo do Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontrar um cenário que corresponda ao seu caso de utilização na [postagens no Blog de JSON](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

