---
title: Emigrar Azure SQL Bases de dados para Azure CosmosDB com Fábrica de Dados Azure
description: Pegue num esquema de base de dados normalizado existente da Base de Dados Azure SQL e emiba para um recipiente desnormalizado Azure CosmosDB com a Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 3d2ef6fb0cd7af444b9bff755eee4eee70d03d15
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691889"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrar esquema de base de dados normalizado da Base de Dados Azure SQL para o contentor desnormalizado Azure CosmosDB

Este guia explicará como pegar num esquema de base de dados normalizado existente na Base de Dados Azure SQL e convertê-lo num esquema desnormalizado do Azure CosmosDB para carregar no Azure CosmosDB.

Os schemas SQL são tipicamente modelados usando a terceira forma normal, resultando em esquemas normalizados que fornecem altos níveis de integridade de dados e menos valores de dados duplicados. As consultas podem juntar entidades através das mesas para leitura. CosmosDB está otimizado para transações super rápidas e consulta dentro de uma recolha ou contentor através de esquemas desnormalizados com dados auto-contidos dentro de um documento.

Utilizando a Azure Data Factory, vamos construir um oleoduto que utiliza um único Fluxo de Dados de Mapeamento para ler a partir de duas tabelas normalizadas da Base de Dados Azure SQL que contêm chaves primárias e estrangeiras como a relação da entidade. A ADF juntar-se-á a essas tabelas num único fluxo utilizando o motor Spark de fluxo de dados, recolherá linhas unidas em matrizes e produzirá documentos individualmente limpos para inserir num novo recipiente Azure CosmosDB.

Este guia irá construir um novo recipiente na mosca ```SalesOrderHeader``` chamado ```SalesOrderDetail``` "encomendas" que utilizará as e tabelas da base de dados padrão da amostra SQL Server AdventureWorks. Estas tabelas representam transações ```SalesOrderID```de vendas a que se juntam . Cada registo de detalhes único tem ```SalesOrderDetailID```a sua própria chave primária de . A relação entre o ```1:M```cabeceamento e o detalhe é. Vamos juntar-nos ```SalesOrderID``` à ADF e depois enrolar cada registo de detalhes relacionados numa matriz chamada "detalhe".

A consulta representativa da SQL para este guia é:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

O recipiente CosmosDB resultante incorporará a consulta interna num único documento e será assim:

![Coleção](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+Novo Pipeline** para criar um novo oleoduto.

2. Adicionar uma atividade de fluxo de dados

3. Na atividade de fluxo de dados, selecione Novo fluxo de **dados de mapeamento**.

4. Vamos construir este gráfico de fluxo de dados abaixo

![Gráfico de fluxo de dados](media/data-flow/cosmosb1.png)

5. Defina a fonte para "SourceOrderDetails". Para o conjunto de dados, crie um novo conjunto ```SalesOrderDetail``` de dados azure SQL Database que aponte para a tabela.

6. Defina a fonte para "SourceOrderHeader". Para o conjunto de dados, crie um novo conjunto ```SalesOrderHeader``` de dados azure SQL Database que aponte para a tabela.

7. Na fonte superior, adicione uma transformação de Coluna Derivada após "SourceOrderDetails". Chame a nova transformação "TypeCast". Precisamos dar ```UnitPrice``` a volta à coluna e lançá-la para um tipo de dados duplo para cosmosDB. Definir a fórmula ```toDouble(round(UnitPrice,2))```para: .

8. Adicione outra coluna derivada e chame-a de "MakeStruct". É aqui que vamos criar uma estrutura hierárquica para manter os valores da tabela de detalhes. Lembre-se, ```M:1``` detalhes são uma relação com cabeçalho. Nomeie ```orderdetailsstruct``` a nova estrutura e crie a hierarquia desta forma, definindo cada subcoluna no nome da coluna de entrada:

![Criar Estrutura](media/data-flow/cosmosb9.png)

9. Agora, vamos à fonte de cabeçalho de vendas. Adicione uma transformação de Juntar. Para o lado direito, selecione "MakeStruct". Deixe-o definido para ```SalesOrderID``` unir interiore e escolha para ambos os lados da condição de união.

10. Clique no separador 'Pré-visualização de dados' na nova adesão que adicionou para que possa ver os seus resultados até este ponto. Devia ver todas as linhas de cabeçalho juntas às linhas de detalhe. Este é o resultado da formação da junta a ```SalesOrderID```partir do . Em seguida, combinaremos os detalhes das linhas comuns com os detalhes se estruturam e agregamos as linhas comuns.

![Associar](media/data-flow/cosmosb4.png)

11. Antes de podermos criar as matrizes para desnormalizar estas linhas, primeiro precisamos remover colunas indesejadas e certificar-nos de que os valores de dados corresponderão aos tipos de dados cosmosDB.

12. Adicione uma transformação Select a seguir e detete o mapeamento de campo para se parecer com este:

![Esfregão de coluna](media/data-flow/cosmosb5.png)

13. Agora vamos lançar novamente uma coluna ```TotalDue```de moeda, desta vez. Como fizemos acima no passo 7, ```toDouble(round(TotalDue,2))```definir a fórmula para: .

14. Aqui é onde vamos desnormalizar as linhas agrupando-nos pela chave ```SalesOrderID```comum. Adicione uma transformação agregada ```SalesOrderID```e coloque o grupo em .

15. Na fórmula agregada, adicione uma nova coluna chamada "detalhes" e use esta fórmula ```orderdetailsstruct``` ```collect(orderdetailsstruct)```para recolher os valores na estrutura que criamos anteriormente chamada: .

16. A transformação agregada só irá passar colunas que fazem parte do agregado ou grupo por fórmulas. Por isso, temos de incluir as colunas do cabeçalho de vendas também. Para isso, adicione um padrão de coluna na mesma transformação agregada. Este padrão incluirá todas as outras colunas na saída:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Utilize a sintaxe "esta" nas outras propriedades para que ```first()``` mantenhamos os mesmos nomes de colunas e utilizemos a função como agregado:

![Agregação](media/data-flow/cosmosb6.png)

18. Estamos prontos para terminar o fluxo migratório adicionando uma transformação de afundar. Clique em "novo" ao lado do conjunto de dados e adicione um conjunto de dados CosmosDB que aponta para a sua base de dados CosmosDB. Para a coleção, vamos chamá-lo de "encomendas" e não terá esquemas nem documentos porque será criado no ar.

19. Em Definições de ```\SalesOrderID``` Sink, Chave de Partição e ação de recolha para "recriar". Certifique-se de que o seu separador de mapeamento é assim:

![Definições de sumidouro](media/data-flow/cosmosb7.png)

20. Clique na pré-visualização de dados para se certificar de que está a ver estas 32 linhas definidas para inserir como novos documentos no seu novo recipiente:

![Definições de sumidouro](media/data-flow/cosmosb8.png)

Se tudo estiver bem, está agora pronto para criar um novo oleoduto, adicionar esta atividade de fluxo de dados a esse pipeline e executá-lo. Pode executar a partir de depuração ou de uma execução desencadeada. Após alguns minutos, deverá ter um novo recipiente desnormalizado de encomendas chamado "encomendas" na sua base de dados CosmosDB.

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando transformações de [fluxos](concepts-data-flow-overview.md)de dados de mapeamento.
* [Descarregue o modelo](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) de pipeline completo para este tutorial e importe o modelo para a sua fábrica.
