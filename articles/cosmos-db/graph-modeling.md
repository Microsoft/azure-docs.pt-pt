---
title: Modelação de dados de gráficos para API Azure Cosmos DB Gremlin
description: Aprenda a modelar uma base de dados de gráficos utilizando a API Azure Cosmos DB Gremlin. Este artigo descreve quando usar uma base de dados de gráficos e boas práticas para modelar entidades e relacionamentos.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898314"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelação de dados de gráficos para API Azure Cosmos DB Gremlin

O seguinte documento foi concebido para fornecer recomendações de modelação de dados gráficos. Este passo é vital para garantir a escalabilidade e desempenho de um sistema de base de dados de gráficos à medida que os dados evoluem. Um modelo de dados eficiente é especialmente importante com gráficos de grande escala.

## <a name="requirements"></a>Requisitos

O processo delineado neste guia baseia-se nos seguintes pressupostos:
 * As **entidades** no espaço problemático são identificadas. Estas entidades devem ser consumidas _atomicamente_ por cada pedido. Por outras palavras, o sistema de base de dados não foi concebido para recuperar os dados de uma única entidade em múltiplos pedidos de consulta.
 * Existe uma compreensão dos **requisitos** de leitura e escrita para o sistema de base de dados. Estes requisitos irão orientar as otimizações necessárias para o modelo de dados gráficos.
 * Os princípios do padrão gráfico de [propriedade Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) são bem compreendidos.

## <a name="when-do-i-need-a-graph-database"></a>Quando preciso de uma base de dados de gráficos?

Uma solução de base de dados de gráficos pode ser aplicada da melhor forma se as entidades e relações num domínio de dados tiverem alguma das seguintes características: 

* As entidades estão **altamente ligadas** através de relações descritivas. O benefício neste cenário é o facto de as relações serem persistidas no armazenamento.
* Existem **relações cíclicas** ou **entidades autorreferenciadas.** Este padrão é frequentemente um desafio quando se utilizam bases de dados relacionais ou documentais.
* Há **relações dinâmicas** em evolução entre entidades. Este padrão é especialmente aplicável aos dados hierárquicos ou estruturados por árvores com muitos níveis.
* Há **muitas ou muitas relações** entre entidades.
* Há **requisitos de escrita e leitura tanto em entidades como em relacionamentos.** 

Se os critérios acima indicados forem satisfeitos, é provável que uma abordagem de base de dados de gráficos proporcione vantagens para a complexidade da **consulta,** **a escalabilidade**do modelo de dados e o desempenho da **consulta.**

O próximo passo é determinar se o gráfico vai ser usado para fins analíticos ou transacionais. Se o gráfico se destina a ser utilizado para cargas de trabalho de computação pesada e processamento de dados, valeria a pena explorar o [conector Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) e a utilização da [biblioteca GraphX.](https://spark.apache.org/graphx/) 

## <a name="how-to-use-graph-objects"></a>Como usar objetos gráficos

O padrão de gráfico de [propriedade Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) define dois tipos de objetos **Vertices** e **Edges**. 

Seguem-se as melhores práticas para as propriedades nos objetos gráficos:

| Object | Propriedade | Tipo | Notas |
| --- | --- | --- |  --- |
| Vértice | ID | String | Aplicada exclusivamente por partição. Se um valor não for fornecido após a inserção, será armazenado um GUID gerado automaticamente. |
| Vértice | label | String | Esta propriedade é usada para definir o tipo de entidade que o vértice representa. Se um valor não for fornecido, será utilizado um "vértice" de valor predefinido. |
| Vértice | propriedades | String, Boolean, Numeric | Uma lista de propriedades separadas armazenadas como pares de valor-chave em cada vértice. |
| Vértice | chave de partição | String, Boolean, Numeric | Esta propriedade define onde o vértice e as suas bordas de saída serão armazenadas. Leia mais sobre [a partilha de gráficos](graph-partitioning.md). |
| Edge | ID | String | Aplicada exclusivamente por partição. Gerado automaticamente por padrão. As bordas geralmente não têm a necessidade de ser recuperadas exclusivamente por um ID. |
| Edge | label | String | Esta propriedade é usada para definir o tipo de relação que dois vértices têm. |
| Edge | propriedades | String, Boolean, Numeric | Uma lista de propriedades separadas armazenadas como pares de valor-chave em cada borda. |

> [!NOTE]
> As bordas não requerem um valor-chave de divisória, uma vez que o seu valor é automaticamente atribuído com base no seu vértice de origem. Saiba mais no artigo de partilha de [gráficos.](graph-partitioning.md)

## <a name="entity-and-relationship-modeling-guidelines"></a>Diretrizes de modelação de entidades e relacionamentos

Seguem-se um conjunto de diretrizes para abordar a modelação de dados para uma base de dados de gráficos API Azure Cosmos DB Gremlin. Estas diretrizes assumem que existe uma definição existente de um domínio de dados e consultas para ele.

> [!NOTE]
> Os passos a seguir descritos são apresentados como recomendações. O modelo final deve ser avaliado e testado antes da sua consideração como pronto para a produção. Além disso, as recomendações abaixo são específicas para a implementação da API Gremlin da Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Vertios e propriedades de modelação 

O primeiro passo para um modelo de dados gráficos é mapear todas as entidades identificadas para um **objeto vértice.** Um mapeamento de um a um de todas as entidades aos vértices deve ser um passo inicial e sujeito a alterações.

Uma armadilha comum é mapear propriedades de uma única entidade como vértices separados. Considere o exemplo abaixo, onde a mesma entidade está representada de duas maneiras diferentes:

* **Propriedades baseadas em Vertex**: Nesta abordagem, a entidade utiliza três vértices e duas bordas separadas para descrever as suas propriedades. Embora esta abordagem possa reduzir o despedimento, aumenta a complexidade dos modelos. Um aumento da complexidade do modelo pode resultar em latência adicional, complexidade de consulta e custo de cálculo. Este modelo também pode apresentar desafios na partilha.

![Modelo de entidade com vértices para propriedades.](./media/graph-modeling/graph-modeling-1.png)

* **Vértices incorporados**na propriedade : Esta abordagem tira partido da lista de pares de valor-chave para representar todas as propriedades da entidade dentro de um vértice. Esta abordagem proporciona uma complexidade de modelo reduzida, o que conduzirá a consultas mais simples e a travessias mais económicas.

![Modelo de entidade com vértices para propriedades.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Os exemplos acima mostram um modelo de gráfico simplificado para apenas mostrar a comparação entre as duas formas de dividir propriedades da entidade.

O padrão **de vértices incorporados na propriedade** geralmente fornece uma abordagem mais performativa e escalável. A abordagem padrão de um novo modelo de dados gráficos deve gravitar para este padrão.

No entanto, existem cenários em que a referência a uma propriedade pode proporcionar vantagens. Por exemplo: se a propriedade referenciada for atualizada frequentemente. A utilização de um vértice separado para representar uma propriedade que é constantemente alterada minimizaria a quantidade de operações de escrita que a atualização exigiria.

### <a name="relationship-modeling-with-edge-directions"></a>Modelação de relacionamento com direções de borda

Após a modelação dos vértices, as bordas podem ser adicionadas para denotar as relações entre eles. O primeiro aspeto que precisa de ser avaliado é a **direção da relação.** 

Os objetos de borda têm uma direção predefinida que é seguida por uma travessia quando se utiliza a função `out()` ou `outE()`. A utilização desta direção natural resulta numa operação eficiente, uma vez que todos os vértices são armazenados com as suas bordas de saída. 

No entanto, atravessar na direção oposta de uma borda, utilizando a função `in()`, resultará sempre numa consulta de divisória cruzada. Saiba mais sobre [a partilha de gráficos.](graph-partitioning.md) Se houver necessidade de atravessar constantemente usando a função `in()`, é aconselhável adicionar arestas em ambas as direções.

Pode determinar a direção da borda utilizando os predicados `.to()` ou `.from()` para o `.addE()` passo Gremlin. Ou usando a biblioteca de [executor a granel para gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Os objetos de borda têm uma direção por defeito.

### <a name="relationship-labeling"></a>Rotulagem de relacionamento

A utilização de etiquetas de relacionamento descritivas pode melhorar a eficiência das operações de resolução de bordas. Este padrão pode ser aplicado das seguintes formas:
* Use termos não genéricos para rotular uma relação.
* Associe o rótulo do vértice de origem ao rótulo do vértice-alvo com o nome da relação.

![Exemplos de rotulagem de relacionamentos.](./media/graph-modeling/graph-modeling-3.png)

Quanto mais específica for a etiqueta que o transísta usará para filtrar as bordas, melhor. Esta decisão pode ter um impacto significativo também no custo da consulta. Pode avaliar o custo da consulta a qualquer momento [utilizando o passo de execuçãoPerfil](graph-execution-profile.md).


## <a name="next-steps"></a>Passos seguintes: 
* Confira a lista de [passos Gremlin](gremlin-support.md)suportados.
* Aprenda sobre a partilha de bases de dados de [gráficos](graph-partitioning.md) para lidar com gráficos em larga escala.
* Avalie as suas consultas de Gremlin utilizando o passo do Perfil de [Execução](graph-execution-profile.md).
