---
title: Modelagem de dados de grafo para Azure Cosmos DB API Gremlin
description: Saiba como modelar um banco de dados de grafo usando Cosmos DB API Gremlin.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 94df90db4a715d2540dfc5ec0aa521d76d22f757
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624218"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelagem de dados de grafo para Azure Cosmos DB API Gremlin

O documento a seguir foi projetado para fornecer recomendações de modelagem de dados de grafo. Essa etapa é vital para garantir a escalabilidade e o desempenho de um sistema de banco de dados de grafo à medida que o dado evolui. Um modelo de dados eficiente é especialmente importante em gráficos de grande escala.

## <a name="requirements"></a>Requisitos

O processo descrito neste guia se baseia nas seguintes suposições:
 * As **entidades** no espaço de problema são identificadas. Essas entidades devem ser consumidas _atomicamente_ para cada solicitação. Em outras palavras, o sistema de banco de dados não foi projetado para recuperar dados de uma única entidade em várias solicitações de consulta.
 * Há uma compreensão dos **requisitos de leitura e gravação** para o sistema de banco de dados. Esses requisitos guiarão as otimizações necessárias para o modelo de dados do grafo.
 * Os princípios do [padrão de gráfico de propriedades do Apache Tinkerpop](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) são bem compreendidos.

## <a name="when-do-i-need-a-graph-database"></a>Quando preciso de um banco de dados de grafo?

Uma solução de banco de dados de grafo pode ser aplicada de forma ideal se as entidades e relações em um domínio de data tiverem qualquer uma das seguintes características: 

* As entidades são **altamente conectadas** por meio de relações descritivas. O benefício nesse cenário é o fato de que as relações são mantidas no armazenamento.
* Há **relações cíclicas** ou **entidades de referência automática**. Esse padrão é geralmente um desafio ao usar bancos de dados relacionais ou de documentos.
* Há **relações dinamicamente em evolução** entre entidades. Esse padrão é especialmente aplicável a dados hierárquicos ou estruturados em árvore com muitos níveis.
* Há **relações muitos para muitos** entre entidades.
* Há **requisitos de gravação e leitura em ambas as entidades e relações**. 

Se os critérios acima forem atendidos, é provável que uma abordagem de banco de dados de grafo forneça vantagens para a **complexidade da consulta**, escalabilidade do **modelo**e **desempenho de consulta**.

A próxima etapa é determinar se o grafo será usado para fins analíticos ou transacionais. Se o grafo se destinar a ser usado para grandes cargas de trabalho de processamento de dados e computação, seria importante explorar o [conector Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) e o uso da [biblioteca GraphX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Como usar objetos de grafo

O [gráfico de propriedades do Apache Tinkerpop padrão](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) define dois tipos de vértices de objetos e **bordas**. 

A seguir estão as práticas recomendadas para as propriedades nos objetos de grafo:

| Objeto | Propriedade | Type | Notas |
| --- | --- | --- |  --- |
| Deles | id | Cadeia | Imposto exclusivamente por partição. Se um valor não for fornecido na inserção, e o GUID gerado automaticamente será armazenado. |
| Deles | label | Cadeia | Essa propriedade é usada para definir o tipo de entidade que o vértice representa. Se um valor não for fornecido, um valor padrão "Vertex" será usado. |
| Deles | properties | Cadeia de caracteres, booliano, numérico | Uma lista de propriedades separadas armazenadas como pares de chave-valor em cada vértice. |
| Deles | chave de partição | Cadeia de caracteres, booliano, numérico | Esta propriedade define onde o vértice e suas bordas de saída serão armazenados. Leia mais sobre [particionamento de grafo](graph-partitioning.md). |
| Microsoft Edge | id | Cadeia | Imposto exclusivamente por partição. Gerado automaticamente por padrão. As bordas geralmente não têm a necessidade de serem recuperadas de forma exclusiva por uma ID. |
| Microsoft Edge | label | Cadeia | Essa propriedade é usada para definir o tipo de relação que dois vértices têm. |
| Microsoft Edge | properties | Cadeia de caracteres, booliano, numérico | Uma lista de propriedades separadas armazenadas como pares de chave-valor em cada borda. |

> [!NOTE]
> As bordas não exigem um valor de chave de partição, já que seu valor é atribuído automaticamente com base no seu vértice de origem. Saiba mais no artigo [particionamento de grafo](graph-partitioning.md) .

## <a name="entity-and-relationship-modeling-guidelines"></a>Diretrizes de modelagem de entidade e relação

Veja a seguir um conjunto de diretrizes para abordar a modelagem de dados para um banco de Gremlin de gráfico de API do Azure Cosmos DB. Essas diretrizes pressupõem que há uma definição existente de um domínio de dados e consultas para ele.

> [!NOTE]
> As etapas descritas abaixo são apresentadas como recomendações. O modelo final deve ser avaliado e testado antes de sua consideração como pronto para produção. Além disso, as recomendações abaixo são específicas para a implementação da API Gremlin do Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Modelando vértices e propriedades 

A primeira etapa para um modelo de dados de grafo é mapear cada entidade identificada para um **objeto de vértice**. Um mapeamento de um para um de todas as entidades para vértices deve ser uma etapa inicial e estar sujeito a alterações.

Uma armadilha comum é mapear as propriedades de uma única entidade como vértices separados. Considere o exemplo abaixo, em que a mesma entidade é representada de duas maneiras diferentes:

* **Propriedades baseadas em vértice**: Nessa abordagem, a entidade usa três vértices separados e duas bordas para descrever suas propriedades. Embora essa abordagem possa reduzir a redundância, ela aumenta a complexidade do modelo. Um aumento na complexidade do modelo pode resultar na latência adicional, na complexidade da consulta e no custo de computação. Esse modelo também pode apresentar desafios no particionamento.

![Modelo de entidade com vértices para propriedades.](./media/graph-modeling/graph-modeling-1.png)

* **Vértices de propriedade inserida**: Essa abordagem aproveita a lista de pares chave-valor para representar todas as propriedades da entidade dentro de um vértice. Essa abordagem fornece redução da complexidade do modelo, o que levará a consultas mais simples e a percorridas mais eficientes.

![Modelo de entidade com vértices para propriedades.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Os exemplos acima mostram um modelo de gráfico simplificado para mostrar apenas a comparação entre as duas maneiras de dividir as propriedades da entidade.

O padrão de **vértices inserido em Propriedade** geralmente fornece uma abordagem mais funcional e escalonável. A abordagem padrão para um novo modelo de dados de grafo deve gravitam em direção a esse padrão.

No entanto, há cenários em que a referência a uma propriedade pode fornecer vantagens. Por exemplo: se a propriedade referenciada for atualizada com frequência. Usar um vértice separado para representar uma propriedade que é constantemente alterada minimizaria a quantidade de operações de gravação exigidas pela atualização.

### <a name="relationship-modeling-with-edge-directions"></a>Modelagem de relação com direções de borda

Depois que os vértices são modelados, as bordas podem ser adicionadas para denotar as relações entre eles. O primeiro aspecto que precisa ser avaliado é a **direção da relação**. 

Os objetos de borda têm uma direção padrão seguida por uma passagem ao usar a `out()` função ou. `outE()` Usar essa direção natural resulta em uma operação eficiente, pois todos os vértices são armazenados com suas bordas de saída. 

No entanto, percorrer a direção oposta de uma borda, usando a `in()` função, resultará sempre em uma consulta entre partições. Saiba mais sobre o [particionamento de grafo](graph-partitioning.md). Se houver a necessidade de percorrer constantemente o uso da `in()` função, é recomendável adicionar bordas em ambas as direções.

Você pode determinar a direção da borda usando os `.to()` predicados ou `.from()` para `.addE()` a etapa Gremlin. Ou usando a [biblioteca de executores em massa para a API Gremlin](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Os objetos de borda têm uma direção por padrão.

### <a name="relationship-labeling"></a>Rotulagem de relações

O uso de rótulos de relacionamento descritivos pode melhorar a eficiência das operações de resolução de borda. Esse padrão pode ser aplicado das seguintes maneiras:
* Use termos não genéricos para rotular uma relação.
* Associe o rótulo do vértice de origem ao rótulo do vértice de destino com o nome da relação.

![Exemplos de rótulos de relacionamento.](./media/graph-modeling/graph-modeling-3.png)

Quanto mais específico o rótulo que a passagem usará para filtrar as bordas, melhor. Essa decisão pode ter um impacto significativo no custo da consulta também. Você pode avaliar o custo da consulta a qualquer momento [usando a etapa executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Passos seguintes: 
* Confira a lista de [etapas de Gremlin](gremlin-support.md)com suporte.
* Saiba mais sobre o [particionamento de banco de dados de grafo](graph-partitioning.md) para lidar com grafos de grande escala.
* Avalie suas consultas do Gremlin usando a [etapa perfil de execução](graph-execution-profile.md).
