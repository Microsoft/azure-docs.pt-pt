---
title: Modelação de dados de gráficos para Azure Cosmos DB Gremlin API
description: Saiba como modelar uma base de dados de gráficos utilizando a API API API AZure Cosmos DB Gremlin. Este artigo descreve quando usar uma base de dados de gráficos e as melhores práticas para modelar entidades e relacionamentos.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 12/02/2019
ms.author: jasonh
ms.openlocfilehash: 6526119a8b20a7c60879fe690aefe96159b062a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409770"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelação de dados de gráficos para Azure Cosmos DB Gremlin API

O documento a seguir destina-se a fornecer recomendações de modelação de dados de gráficos. Este passo é vital para garantir a escalabilidade e desempenho de um sistema de base de dados de gráficos à medida que os dados evoluem. Um modelo de dados eficiente é especialmente importante com gráficos em larga escala.

## <a name="requirements"></a>Requisitos

O processo delineado neste guia baseia-se nos seguintes pressupostos:
 * As **entidades** do espaço-problema são identificadas. Estas entidades devem ser consumidas _atomicamente_ para cada pedido. Por outras palavras, o sistema de base de dados não foi concebido para recuperar os dados de uma única entidade em múltiplos pedidos de consulta.
 * Existe uma compreensão dos requisitos de leitura e de escrita para o sistema de base de **dados.** Estes requisitos orientarão as otimizações necessárias para o modelo de dados gráficos.
 * Os princípios do padrão de [gráfico de propriedade Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) são bem compreendidos.

## <a name="when-do-i-need-a-graph-database"></a>Quando preciso de uma base de dados de gráficos?

Uma solução de base de dados de gráficos pode ser aplicada da melhor forma se as entidades e relações num domínio de dados tiverem alguma das seguintes características: 

* As entidades estão **altamente ligadas** através de relações descritivas. O benefício neste cenário é o facto de as relações persistirem no armazenamento.
* Existem **relações cíclicas** ou **entidades autorreferenciadas.** Este padrão é muitas vezes um desafio quando se utilizam bases de dados relacionais ou documentais.
* Existem **relações dinâmicas em evolução** entre entidades. Este padrão é especialmente aplicável a dados hierárquicos ou estruturados em árvores com muitos níveis.
* Há **muitas relações** entre entidades.
* Existem **requisitos de escrita e leitura tanto nas entidades como nas relações.** 

Se os critérios acima referidos forem satisfeitos, é provável que uma abordagem de base de dados de gráficos proporcione vantagens para **a complexidade da consulta,** **escalabilidade do modelo de dados**e **desempenho de consulta**.

O próximo passo é determinar se o gráfico vai ser usado para fins analíticos ou transacionais. Se o gráfico se destinar a ser utilizado para trabalhos pesados de cálculo e processamento de dados, valeria a pena explorar o [conector Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) e a utilização da [biblioteca GraphX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Como usar objetos gráficos

O [padrão de gráfico de propriedade Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) define dois tipos de objetos **Vértices** e **Bordas.** 

Seguem-se as melhores práticas para as propriedades nos objetos gráficos:

| Objeto | Propriedade | Tipo | Notas |
| --- | --- | --- |  --- |
| Vértex | ID | Cadeia | Exclusivamente aplicada por divisória. Se um valor não for fornecido após a inserção, será armazenado um GUID gerado automaticamente. |
| Vértex | etiqueta | Cadeia | Esta propriedade é usada para definir o tipo de entidade que o vértice representa. Se um valor não for fornecido, será utilizado um "vértice" de valor predefinido. |
| Vértex | propriedades | String, Boolean, Numérico | Uma lista de propriedades separadas armazenadas como pares de valor-chave em cada vértice. |
| Vértex | chave de partição | String, Boolean, Numérico | Esta propriedade define onde o vértice e as suas bordas de saída serão armazenados. Leia mais sobre [a partição de gráficos.](graph-partitioning.md) |
| Microsoft Edge | ID | Cadeia | Exclusivamente aplicada por divisória. Gerado automaticamente por defeito. As bordas geralmente não têm a necessidade de ser recuperadas exclusivamente por um ID. |
| Microsoft Edge | etiqueta | Cadeia | Esta propriedade é usada para definir o tipo de relacionamento que dois vértices têm. |
| Microsoft Edge | propriedades | String, Boolean, Numérico | Uma lista de propriedades separadas armazenadas como pares de valores-chave em cada borda. |

> [!NOTE]
> As bordas não requerem um valor chave de partição, uma vez que o seu valor é automaticamente atribuído com base no seu vértice de origem. Saiba mais no artigo [de partição de gráficos.](graph-partitioning.md)

## <a name="entity-and-relationship-modeling-guidelines"></a>Diretrizes de modelação de entidades e relacionamentos

Seguem-se um conjunto de diretrizes para abordar a modelação de dados para uma base de dados de gráficos AZure Cosmos DB Gremlin API. Estas diretrizes assumem que há uma definição existente de um domínio de dados e consultas para isso.

> [!NOTE]
> Os passos a seguir descritos são apresentados como recomendações. O modelo final deve ser avaliado e testado antes da sua consideração como pronto para a produção. Além disso, as recomendações abaixo são específicas para a implementação da API gremlin da Azure Cosmos. 

### <a name="modeling-vertices-and-properties"></a>Modelação de vértices e propriedades 

O primeiro passo para um modelo de dados gráficos é mapear todas as entidades identificadas para um **objeto vértice**. Um a um mapeamento de todas as entidades para vértices deve ser um passo inicial e sujeito a mudança.

Uma armadilha comum é mapear propriedades de uma única entidade como vértices separados. Considere o exemplo abaixo, onde a mesma entidade está representada de duas maneiras diferentes:

* **Propriedades baseadas em vértice**: Nesta abordagem, a entidade utiliza três vértices separados e duas bordas para descrever as suas propriedades. Embora esta abordagem possa reduzir a redundância, aumenta a complexidade do modelo. Um aumento da complexidade do modelo pode resultar em latência adicional, complexidade de consulta e custo de computação. Este modelo também pode apresentar desafios na partição.

:::image type="content" source="./media/graph-modeling/graph-modeling-1.png" alt-text="Modelo de entidade com vértices para propriedades." border="false":::

* **Vértices incorporados à propriedade**: Esta abordagem tira partido da lista de pares de valores-chave para representar todas as propriedades da entidade dentro de um vértice. Esta abordagem proporciona uma complexidade reduzida do modelo, o que levará a consultas mais simples e a travessias mais económicas.

:::image type="content" source="./media/graph-modeling/graph-modeling-2.png" alt-text="Modelo de entidade com vértices para propriedades." border="false":::

> [!NOTE]
> Os exemplos acima mostram um modelo de gráfico simplificado para mostrar apenas a comparação entre as duas formas de dividir as propriedades da entidade.

O padrão **de vértices incorporados na propriedade** geralmente proporciona uma abordagem mais performante e escalável. A abordagem padrão de um novo modelo de dados de gráficos deve gravitar para este padrão.

No entanto, existem cenários em que a referência a uma propriedade pode proporcionar vantagens. Por exemplo: se a propriedade referenciada for atualizada frequentemente. Usar um vértice separado para representar uma propriedade que é constantemente alterada minimizaria a quantidade de operações de escrita que a atualização exigiria.

### <a name="relationship-modeling-with-edge-directions"></a>Modelação de relacionamento com direções de borda

Depois de modelados os vértices, as bordas podem ser adicionadas para denotar as relações entre eles. O primeiro aspeto que precisa de ser avaliado é a **direção da relação.** 

Os objetos de borda têm uma direção predefinitiva que é seguida por um traverso ao utilizar o `out()` ou `outE()` a função. A utilização desta direção natural resulta numa operação eficiente, uma vez que todos os vértices são armazenados com as suas bordas de saída. 

No entanto, atravessar na direção oposta de uma borda, utilizando a `in()` função, resultará sempre numa consulta de divisórias cruzadas. Saiba mais sobre [a partição de gráficos.](graph-partitioning.md) Se houver necessidade de atravessar constantemente usando a `in()` função, é aconselhável adicionar arestas em ambas as direções.

Pode determinar a direção da borda utilizando os `.to()` `.from()` predicados ou predicados no `.addE()` degrau Gremlin. Ou utilizando a biblioteca de [executores a granel para a Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Os objetos de borda têm uma direção por defeito.

### <a name="relationship-labeling"></a>Rotulagem de relacionamento

A utilização de rótulos de relacionamento descritivos pode melhorar a eficiência das operações de resolução de arestas. Este padrão pode ser aplicado das seguintes formas:
* Use termos não genéricos para rotular uma relação.
* Associar o rótulo do vértice de origem ao rótulo do vértice-alvo com o nome da relação.

:::image type="content" source="./media/graph-modeling/graph-modeling-3.png" alt-text="Modelo de entidade com vértices para propriedades." border="false":::

Quanto mais específica o rótulo que o atravessador utilizar para filtrar as bordas, melhor. Esta decisão pode ter um impacto significativo também no custo da consulta. Pode avaliar o custo da consulta a qualquer momento [utilizando o passo de execuçãoProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Passos seguintes: 
* Confira a lista de [passos gremlin suportados.](gremlin-support.md)
* Saiba mais sobre [a partição da base de dados](graph-partitioning.md) de gráficos para lidar com gráficos em larga escala.
* Avalie as suas consultas gremlin utilizando o [passo do Perfil de Execução](graph-execution-profile.md).
