---
title: Migrar as tabelas de base de dados do Azure SQL para Azure CosmosDB com Azure Data Factory
description: Pegue um esquema de base de dados normalizado existente a partir da Base de Dados Azure SQL e migra para um recipiente desnormalizado Azure CosmosDB com Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383947"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrar o esquema da base de dados normalizada da Base de Dados SQL do Azure para um contentor desnormalizado do Azure CosmosDB

Este guia explicará como pegar num esquema de base de dados normalizado existente na Base de Dados Azure SQL e convertê-lo num esquema desnormalizado Azure CosmosDB para carregar em Azure CosmosDB.

Os esquemas SQL são tipicamente modelados usando a terceira forma normal, resultando em esquemas normalizados que fornecem altos níveis de integridade dos dados e menos valores duplicados de dados. As consultas podem juntar entidades através das tabelas para leitura. CosmosDB é otimizado para transações super-rápidas e consulta dentro de uma recolha ou recipiente através de esquemas desnormalizados com dados autossuficientes dentro de um documento.

Utilizando a Azure Data Factory, construiremos um oleoduto que utiliza um único Fluxo de Dados de Mapeamento para ler a partir de duas tabelas normalizadas da Base de Dados Azure SQL que contêm chaves primárias e estrangeiras como a relação da entidade. A ADF juntará essas tabelas num único fluxo usando o motor de fluxo de dados Spark, recolherá linhas unidas em matrizes e produzirá documentos limpos individuais para inserir num novo recipiente Azure CosmosDB.

Este guia irá construir um novo recipiente na mosca chamado "encomendas" que utilizará as ```SalesOrderHeader``` tabelas e ```SalesOrderDetail``` tabelas a partir da base de dados padrão de amostras SQL Server AdventureWorks. Estas tabelas representam transações de vendas a que se ```SalesOrderID``` juntam. Cada registo de detalhes único tem a sua própria chave primária ```SalesOrderDetailID``` de. A relação entre cabeçalho e detalhe é ```1:M``` . Vamos juntar-nos à ```SalesOrderID``` ADF e depois lançar cada registo de detalhes relacionados numa matriz chamada "detalhe".

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

2. Adicione uma atividade de fluxo de dados

3. Na atividade de fluxo de dados, selecione **Novo fluxo de dados de mapeamento**.

4. Vamos construir este gráfico de fluxo de dados abaixo

![Gráfico de fluxo de dados](media/data-flow/cosmosb1.png)

5. Defina a fonte para "SourceOrderDetails". Para conjunto de dados, crie um novo conjunto de dados da Base de Dados Azure SQL que aponta para a ```SalesOrderDetail``` tabela.

6. Defina a fonte para "SourceOrderHeader". Para conjunto de dados, crie um novo conjunto de dados da Base de Dados Azure SQL que aponta para a ```SalesOrderHeader``` tabela.

7. Na fonte superior, adicione uma transformação da Coluna Derivada após "SourceOrderDetails". Chame a nova transformação de "TypeCast". Precisamos dar a volta à ```UnitPrice``` coluna e lanhá-la para um tipo de dados duplo para cosmosDB. Desa esta medida: ```toDouble(round(UnitPrice,2))``` .

8. Adicione outra coluna derivada e chame-a de "MakeStruct". É aqui que vamos criar uma estrutura hierárquica para manter os valores da tabela de detalhes. Lembre-se, os detalhes são uma ```M:1``` relação com o cabeçalho. Nomeie a nova estrutura ```orderdetailsstruct``` e crie a hierarquia desta forma, definindo cada subcolumn para o nome da coluna de entrada:

![Criar Estrutura](media/data-flow/cosmosb9.png)

9. Agora, vamos para a fonte de cabeçalho de vendas. Adicione uma transformação de Join. Para a seleção do lado direito "MakeStruct". Deixe-o definido para a união interior e escolha ```SalesOrderID``` para ambos os lados da condição de junção.

10. Clique no separador Desatenção de Dados na nova junção que adicionou para que possa ver os seus resultados até este ponto. Devias ver todas as linhas de cabeçalho juntas com as linhas de pormenor. Este é o resultado da junção que foi formada a partir do ```SalesOrderID``` . Em seguida, combinaremos os detalhes das linhas comuns para os detalhes estruturam e agregaremos as linhas comuns.

![Participar](media/data-flow/cosmosb4.png)

11. Antes de podermos criar as matrizes para desnormalizar estas linhas, primeiro precisamos remover colunas indesejadas e certificar-nos de que os valores de dados vão corresponder aos tipos de dados do CosmosDB.

12. Adicione uma transformação Select em seguida e desem este conjunto o mapeamento de campo para ficar assim:

![Esfregão de coluna](media/data-flow/cosmosb5.png)

13. Agora vamos lançar novamente uma coluna de moeda, desta ```TotalDue``` vez. Como fizemos acima no passo 7, definimos a fórmula para: ```toDouble(round(TotalDue,2))``` .

14. Aqui é onde vamos desnormalizar as linhas agrupar-nos pela chave ```SalesOrderID``` comum. Adicione uma transformação agregada e coloque o grupo por ```SalesOrderID``` .

15. Na fórmula agregada, adicione uma nova coluna chamada "detalhes" e use esta fórmula para recolher os valores na estrutura que criamos anteriormente ```orderdetailsstruct``` chamados : ```collect(orderdetailsstruct)``` .

16. A transformação agregada só irá fazer parte de colunas agregadas ou agrupas por fórmulas. Por isso, temos de incluir as colunas do cabeçalho de vendas também. Para isso, adicione um padrão de coluna nessa mesma transformação agregada. Este padrão incluirá todas as outras colunas na saída:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Utilize a sintaxe "esta" nas outras propriedades para que mantenhamos os mesmos nomes de colunas e utilizemos a ```first()``` função como agregado:

![Agregação](media/data-flow/cosmosb6.png)

18. Estamos prontos para terminar o fluxo de migração adicionando uma transformação de sumidouro. Clique em "novo" ao lado do conjunto de dados e adicione um conjunto de dados CosmosDB que aponta para a sua base de dados CosmosDB. Para a coleção, vamos chamá-lo de "encomendas" e não terá esquema nem documentos porque será criado no voo.

19. Nas Definições de Sink, Chave de partição ```\SalesOrderID``` e ação de recolha para "recriar". Certifique-se de que o separador de mapeamento é assim:

![A imagem mostra o separador Mapping.](media/data-flow/cosmosb7.png)

20. Clique na pré-visualização de dados para se certificar de que está a ver estas 32 linhas definidas para inserir como novos documentos no seu novo recipiente:

![A screenshot mostra o separador de pré-visualização de dados.](media/data-flow/cosmosb8.png)

Se tudo estiver bem, está agora pronto para criar um novo oleoduto, adicione esta atividade de fluxo de dados a esse oleoduto e execute-o. Pode executar a partir de depurado ou uma corrida desencadeada. Após alguns minutos, deverá ter um novo recipiente de encomendas desnormalizado chamado "encomendas" na sua base de dados CosmosDB.

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
* [Descarregue o modelo de pipeline completo](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) para este tutorial e importe o modelo para a sua fábrica.
