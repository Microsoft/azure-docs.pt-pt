---
title: Dados de gráficos de modelagem para a API de Gremlin do Azure Cosmos DB
description: Saiba como modelar uma base de dados usando a API do Gremlin do Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: c6ae23efa90874bbefc2aff35f8798aa6c0da791
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503421"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Dados de gráficos de modelagem para a API de Gremlin do Azure Cosmos DB

O seguinte documento foi concebido para fornecer recomendações de modelagem de dados do gráfico. Este passo é vital para garantir a escalabilidade e desempenho de um sistema de base de dados de gráfico à medida que os dados evolui. Um modelo de dados eficiente é especialmente importante com gráficos em grande escala.

## <a name="requirements"></a>Requisitos

O processo descrito neste guia baseia-se as seguintes suposições:
 * O **entidades** no espaço de problema são identificados. Essas entidades se destinam a ser consumido _atomicamente_ para cada solicitação. Em outras palavras, o sistema de base de dados não é projetado para recuperar dados de uma única entidade em vários pedidos de consulta.
 * Existe uma compreensão dos **ler e escrever requisitos** para o sistema de base de dados. Estes requisitos irão orientá as otimizações necessárias para o modelo de dados do gráfico.
 * Os princípios do [gráfico de propriedade do Apache Tinkerpop padrão](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) são bem compreendidos.

## <a name="when-do-i-need-a-graph-database"></a>Quando precisa de uma base de dados?

Uma solução de base de dados do gráfico pode ser aplicada de forma ideal se as entidades e relações num domínio de dados tem qualquer uma das seguintes características: 

* As entidades são **altamente ligados** por meio de relações descritivas. A vantagem neste cenário é o fato de que as relações são mantidas no armazenamento.
* Existem **relações cíclicas** ou **Self-referenciado entidades**. Este padrão, muitas vezes, é um desafio quando utilizar relacional ou bases de dados de documentos.
* Existem **evoluir de forma dinâmica as relações** entre entidades. Este padrão é especialmente aplicável aos dados hierárquicos ou estruturado de árvore com vários níveis.
* Existem **relações muitos-para-muitos** entre entidades.
* Existem **escrever e ler os requisitos em entidades e relações**. 

Se os critérios acima for satisfeita, é provável que uma abordagem de base de dados do gráfico irá fornecer vantagens para **consultar complexidade**, **escalabilidade do modelo de dados**, e **dedesempenhodeconsultas**.

A próxima etapa é determinar se o gráfico será a ser utilizado para fins analíticos ou transacionais. Se o gráfico se destina a ser utilizado para cargas de trabalho de processamento de dados e de computação intensivo, seria que vale a pena explorar os [conector do Spark do Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector) e o uso da [biblioteca GraphX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Como utilizar objetos de gráfico

O [padrão de gráfico de propriedade do Apache Tinkerpop](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) define dois tipos de objetos **vértices** e **margens**. 

Seguem-se as melhores práticas para as propriedades nos objetos de gráfico:

| Object | Propriedade | Tipo | Notas |
| --- | --- | --- |  --- |
| Vertex | id | String | Com exclusividade imposta por partição. Se um valor não for fornecido, após a inserção e gerado automaticamente GUID será armazenado. |
| Vertex | label | String | Esta propriedade é utilizada para definir o tipo de entidade que representa o vértice. Se um valor não for fornecido, será utilizado um valor predefinido "vértice". |
| Vertex | properties | Cadeia de caracteres, booleanos, numérico | Uma lista de propriedades separadas armazenados como pares chave-valor em cada vértice. |
| Vertex | Chave de partição | Cadeia de caracteres, booleanos, numérico | Esta propriedade define onde o vértice e bordas de saída serão armazenadas. Leia mais sobre [criação de partições de gráfico](graph-partitioning.md). |
| Microsoft Edge | id | String | Com exclusividade imposta por partição. Gerado automaticamente por predefinição. Margens, normalmente, não tem a necessidade de forma exclusiva ser recuperados por um ID. |
| Microsoft Edge | label | String | Esta propriedade é utilizada para definir o tipo de relação que tenham dois vértices. |
| Microsoft Edge | properties | Cadeia de caracteres, booleanos, numérico | Uma lista de propriedades separadas armazenados como pares chave-valor em cada extremidade. |

> [!NOTE]
> Margens não necessitam de um valor de chave de partição, uma vez que o seu valor é atribuído automaticamente com base na respetiva vértice de origem. Saiba mais no [criação de partições de gráfico](graph-partitioning.md) artigo.

## <a name="entity-and-relationship-modeling-guidelines"></a>Entidade e diretrizes de modelagem de relação

Seguem-se um conjunto de diretrizes para dados de abordagem de modelagem para uma base de dados do graph API do Azure Cosmos DB Gremlin. Essas diretrizes partem do princípio de que existe uma definição existente de um domínio de dados e consultas para o mesmo.

> [!NOTE]
> Os passos descritos abaixo são apresentados como recomendações. O modelo final deve ser avaliado e testado antes de sua consideração como prontos para produção. Além disso, as recomendações abaixo são específicas para implementação de API do Gremlin do Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Vértices e as propriedades de modelagem 

O primeiro passo para um modelo de dados do gráfico é mapear cada entidade identificada para uma **objeto vértice**. Um mapeamento um para um das todas as entidades para vértices deve ser uma etapa inicial e sujeitos a alteração.

Uma armadilha comum é mapear as propriedades de uma única entidade como vértices separados. Considere o exemplo abaixo, onde a mesma entidade é representada de duas formas diferentes:

* **Propriedades baseadas no vértice**: Nesta abordagem, a entidade utiliza três separados vértices e duas margens para descrever as respetivas propriedades. Embora essa abordagem pode reduzir a redundância, aumenta a complexidade do modelo. Um aumento na complexidade de modelo pode resultar numa latência adicional, complexidade da consulta e o custo de computação. Este modelo também pode apresentar desafios na criação de partições.

![Modelo de entidade com vértices para propriedades.](./media/graph-modeling/graph-modeling-1.png)

* **Vértices incorporado de propriedade**: Essa abordagem aproveita a lista de par chave-valor para representar todas as propriedades da entidade dentro de um vértice. Esta abordagem fornece a complexidade do modelo reduzida, o que levará a consultas mais simples e mais transversais de baixo custo.

![Modelo de entidade com vértices para propriedades.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Os exemplos acima mostram um modelo de gráfico simplificado para mostrar apenas a comparação entre as duas formas de dividir as propriedades de entidade.

O **incorporado de propriedade vértices** padrão geralmente fornece um desempenho mais elevado e abordagem dimensionável. A abordagem padrão para um novo modelo de dados do gráfico deve gravitam para esse padrão.

No entanto, existem cenários em que a referência a uma propriedade poderá fornecer vantagens. Por exemplo: se a propriedade referenciada é atualizada frequentemente. Usando um vértice separado para representar uma propriedade que é alterada constantemente seria minimizar a quantidade de operações de escrita que requer que a atualização.

### <a name="relationship-modeling-with-edge-directions"></a>Relação de modelagem com direções do edge

Depois dos vértices são modelados, as bordas podem ser adicionadas para indicar as relações entre eles. É o primeiro aspeto que tem de ser avaliada a **direção da relação**. 

Objetos de borda tem uma direção predefinida que é seguida por uma transversal ao utilizar o `out()` ou `outE()` função. Usar essa direção natural resulta numa operação eficiente, uma vez que todos os vértices são armazenados com seus limites de saída. 

No entanto, atravessando na direção oposta de um edge, usando o `in()` funcionar, sempre resultará numa consulta entre partições. Saiba mais sobre [criação de partições de gráfico](graph-partitioning.md). Se for necessário para atravessar constantemente com o `in()` função, é recomendado para adicionar margens em ambas as direções.

Pode determinar a direção de borda, utilizando o `.to()` ou `.from()` predicados para o `.addE()` passo do Gremlin. Ou utilizando o [BulkExecutor biblioteca para a API do Gremlin](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Por predefinição, os objetos de borda tem uma direção.

### <a name="relationship-labeling"></a>A etiquetagem de relação

Utilização de etiquetas de relação descritivo pode melhorar a eficiência das operações de resolução de borda. Este padrão pode ser aplicado das seguintes formas:
* Utilize termos não genérica que identifique uma relação.
* Associe a etiqueta do vértice de origem para a etiqueta do vértice de destino com o nome da relação.

![Exemplos de etiquetagem de relação.](./media/graph-modeling/graph-modeling-3.png)

Quanto mais específicas a etiqueta que o traverser irá utilizar para filtrar as bordas, melhor. Esta decisão pode ter um impacto significativo no custo de consulta também. Pode avaliar o custo de consulta em qualquer altura [utilizando o passo de executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Passos seguintes: 
* Verifique a lista de suportadas [passos do Gremlin](gremlin-support.md).
* Saiba mais sobre [criação de partições de base de dados de gráfico](graph-partitioning.md) para lidar com gráficos em grande escala.
* Avalie suas consultas do Gremlin com o [passo de perfil de execução](graph-execution-profile.md).
