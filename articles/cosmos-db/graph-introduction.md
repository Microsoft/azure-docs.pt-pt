---
title: Introdução ao Azure Cosmos DB a API do Gremlin
description: Saiba como pode utilizar o Azure Cosmos DB para armazenar, consultar e percorrer gráficos enormes com baixa latência através da linguagem de consulta de gráficos Gremlin do Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/05/2018
ms.author: lbosq
ms.openlocfilehash: 10565839148eae0db4bb374939eb3725f6262e60
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452951"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introdução ao Azure Cosmos DB: API do Gremlin

O [Azure Cosmos DB](introduction.md) é um serviço de bases de dados com vários modelos e distribuído globalmente pela Microsoft para aplicações críticas para atividades. É uma base de dados multimodal e suporta modelos de dados em documentos, chaves-valores, gráficos e colunas. A API do Gremlin do Azure Cosmos DB é utilizada para armazenar e operar em dados de gráficos. A API do Gremlin suporta a modelação de dados de gráficos e fornece APIs para percorrê-los.

Este artigo fornece uma descrição geral da API do Gremlin do Azure Cosmos DB e explica como pode utilizá-la para armazenar gráficos enormes com milhares de milhões de vértices e margens. Pode consultar os gráficos com latência de milissegundos e desenvolver facilmente o esquema e estrutura do gráfico. Para consultar o Azure Cosmos DB, pode utilizar a linguagem de percurso em gráfico do [Apache TinkerPop](https://tinkerpop.apache.org) ou do [Gremlin](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps).

## <a name="what-is-a-graph-database"></a>O que é uma base de dados de gráficos
Os dados, tal como são apresentados no mundo real, estão ligados naturalmente. A modelação de dados tradicional foca-se nas entidades. Para muitas aplicações, também existe a necessidade de modelar as entidades e relações naturalmente.

Um [gráfico](http://mathworld.wolfram.com/Graph.html) é uma estrutura composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [margens](http://mathworld.wolfram.com/GraphEdge.html). Os vértices e as margens podem ter um número arbitrário de propriedades. 

* **Vértices** - Os vértices denotam objetos discretos, como uma pessoa, local ou evento. 

* **Margens** - As margens denotam as relações entre vértices. Por exemplo, uma pessoa pode conhecer outra pessoa, participar num evento e ter estado numa localização recentemente. 

* **Propriedades** - As propriedades expressam informações sobre os vértices e margens. As propriedades de exemplo incluem um vértice com nome e idade. Uma margem, com carimbo de data/hora e/ou ponderação. Mais formalmente, este modelo é conhecido como um [gráfico de propriedades](https://tinkerpop.apache.org/docs/current/reference/#intro). O Azure Cosmos DB suporta o modelo de gráfico de propriedades.

Por exemplo, o seguinte gráfico de exemplo mostra relações entre pessoas, dispositivos móveis, interesses e sistemas operativos:

![Base de dados de exemplo a mostrar pessoas, dispositivos e interesses](./media/graph-introduction/sample-graph.png)

As bases de dados de gráficos permitem-lhe modelar e armazenar gráficos de forma natural e eficiente, o que os torna úteis para muitos cenários. Normalmente, as bases de dados de gráficos são bases de dados NoSQL, pois estes casos de utilização geralmente precisam de iteração rápida e flexibilidade de esquema.

Pode combinar as transversais rápidas que as bases de dados de gráficos fornecem com algoritmos de gráficos, como Depth-First Search (Pesquisa em Profundidade Primeiro), Breadth-First Search (Pesquisa em Largura Primeiro) e algoritmo de Dijkstra, para resolver problemas em vários domínios, como redes sociais, gestão de conteúdos, geoespaciais e recomendações.

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funcionalidades da base de dados de gráficos do Azure Cosmos DB
 
O Azure Cosmos DB é uma base de dados de gráficos completamente gerida que oferece distribuição global, dimensionamento elástico do débito e armazenamento, consulta e indexação automática, níveis de consistência ajustáveis e suporte para o padrão TinkerPop.

![Arquitetura de gráficos do Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

O Azure Cosmos DB oferece as seguintes funcionalidades diferenciadas em comparação com outras bases de dados de gráficos no mercado:

* Débito e armazenamento dimensionável de forma elástica

 Os gráficos no mundo real precisam de ser dimensionados para além da capacidade de um único servidor. Com o Azure Cosmos DB, pode dimensionar de forma totalmente integrada os seus gráficos em múltiplos servidores. Também pode dimensionar o débito do seu gráfico de forma independente com base nos seus padrões de acesso. O Azure Cosmos DB suporta bases de dados de gráficos que podem ser dimensionadas para débito aprovisionado e tamanhos de armazenamento virtualmente ilimitados.

* Replicação de várias regiões

 O Azure Cosmos DB replica os dados do seu gráfico de forma transparente para todas as regiões associadas à sua conta. A replicação permite-lhe desenvolver aplicações que precisam de acesso global aos dados. É necessário atingir um equilíbrio nas áreas da consistência, disponibilidade, desempenho e garantias correspondentes. O Azure Cosmos DB fornece ativação pós-falha regional transparente com APIs multi-homing. Pode dimensionar o débito e armazenamento de forma elástica em todo o mundo.

* Transversais e consultas rápidas com sintaxe Gremlin que já conhece

 Armazene margens e vértices heterogéneos e consulte esses documentos através de uma sintaxe Gremlin que já conhece. O Azure Cosmos DB utiliza uma tecnologia de indexação estruturada em registos, sem bloqueio e de elevada simultaneidade, para indexar automaticamente todos os conteúdos. Esta funcionalidade permite transversais e consultas avançadas em tempo real sem a necessidade de especificar sugestões de esquema, índices secundários ou vistas. Saiba mais em [Query graphs by using Gremlin](gremlin-support.md) (Consultar gráficos com o Gremlin).

* Totalmente gerido

 O Azure Cosmos DB elimina a necessidade de gerir recursos de máquinas e bases de dados. Uma vez que o serviço Microsoft Azure é completamente gerido, não precisa de gerir máquinas virtuais, implementar e configurar o software, gerir o dimensionamento ou lidar com atualizações de camada de dados complexas. São criadas cópias de segurança automáticas de todos os gráficos e estes são protegidos contra falhas regionais. Pode adicionar facilmente uma conta do Azure Cosmos DB e aprovisionar a capacidade conforme necessário, para que possa focar-se na sua aplicação em vez de operar e gerir a sua base de dados.

* Indexação automática

 Por predefinição, o Azure Cosmos DB indexa automaticamente todas as propriedades dos nós e margens do gráfico e não precisa de nenhum esquema ou criação de índices secundários.

* Compatibilidade com o Apache TinkerPop

 O Azure Cosmos DB suporta de forma nativa o padrão Apache TinkerPop e pode ser integrado com outros sistemas de gráficos compatíveis com o TinkerPop. Assim, pode migrar facilmente de outra base de dados de gráficos, como o Titan ou Neo4j, ou utilizar o Azure Cosmos DB com arquiteturas de análise de gráficos, como o Apache Spark GraphX.

* Níveis de consistência ajustáveis

 Selecione um dos cinco níveis de consistência bem definidos para alcançar um excelente equilíbrio entre a consistência e o desempenho. Para consultas e operações de leitura, o Azure Cosmos DB oferece cinco níveis de consistência distintos: forte, consistência vinculada, sessão, prefixo de consistência e eventual. Estes níveis de consistência granulares e bem definidos permitem-lhe atingir um equilíbrio eficaz entre a consistência, a disponibilidade e a latência. Saiba mais em [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB).

O Azure Cosmos DB também pode utilizar múltiplos modelos, como documentos e gráficos, nos mesmos contentores/bases de dados. Pode utilizar um contentor de documentos para armazenar dados de gráficos lado a lado com documentos. Pode utilizar as consultas SQL sobre as consultas Gremlin e JSON para consultar os mesmos dados como um gráfico.

## <a name="get-started"></a>Introdução

Pode utilizar a interface de linha de comandos (CLI) do Azure, o Azure PowerShell ou o portal do Azure para criar e aceder a contas da API do Gremlin do Azure Cosmos DB. Depois de criar uma conta, pode aceder às bases de dados de gráficos nessa conta através do ponto final de serviço da API do Gremlin `https://<youraccount>.gremlin.cosmosdb.azure.com`, que fornece um front-end de WebSocket para Gremlin. Pode configurar as suas ferramentas compatíveis com o TinkerPop, como a [Consola do Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), para ligar a este ponto final e criar aplicações em Java, Node.js ou qualquer controlador do cliente Gremlin.

A seguinte tabela mostra controladores Gremlin populares que pode utilizar com o Azure Cosmos DB:

| Transferência | Documentação | Introdução | Versão do conector suportado |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Criar Gráficos com .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Documentação JavaDoc do Gremlin](https://tinkerpop.apache.org/javadocs/current/full/) | [Criar Gráficos com Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript no GitHub](https://github.com/jbmusso/gremlin-javascript) | [Criar Gráficos com Node.js](create-graph-nodejs.md) | 2.6.0|
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Criar Gráficos com Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP no GitHub](https://github.com/PommeVerte/gremlin-php) | [Criar Gráficos com PHP](create-graph-php.md) | 3.1.0 |
| [Consola do Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documentação do TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Criar Gráficos na Consola do Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="graph-database-design-considerations"></a>Considerações de design de bases de dados de gráficos

Durante o design de gráfico, a decisão de modelar uma entidade como um vértice do seu próprio, em oposição a como uma propriedade de outras entidades de vértice tem implicações de desempenho e custo. O fator que mais contribui para esta decisão depende da forma como os dados vão ser consultados, bem como da escalabilidade do próprio modelo.

Considere as seguintes perguntas antes de planear sobre como modelar a entidade:

* Quais são as entidades que têm de ser obtidas como vértices para a maioria das consultas?

* Que informações incluídas no gráfico são adicionadas para filtragem de dados?

* Que entidades são meras ligações a outras entidades, que são obtidas para os respetivos valores?

* Que partes de informações da consulta têm de ser obtidas e qual o custo de RU que vão gerar?

Por exemplo, suponha o seguinte design de gráfico:

![Exemplo de considerações de design de gráficos](./media/graph-introduction/graph-design-considerations-example.png)

* Consoante as consultas, é possível que a relação Distrito->Loja seja utilizada exclusivamente para filtrar os vértices Loja. Por exemplo, se as consultas estiverem no formato "obter todas as lojas que pertencem a um distrito específico". Se for este o caso, vale a pena considerar fechar a entidade Distrito num vértice para uma propriedade do vértice Loja. 

* Esta abordagem tem a vantagem de reduzir o custo de cada vértice Loja de obter três objetos de gráfico em simultâneo (Distrito, Distrito->Loja, Loja) para um único vértice Loja. Isto pode fornecer melhorias de desempenho, bem como um custo reduzido por consulta.

* Uma vez que o vértice Loja contém ligações a duas entidades diferentes: Colaborador e Produto. Faz de Loja um vértice necessário porque pode fornecer possibilidades adicionais de percorrer.  



## <a name="scenarios-that-can-use-gremlin-api"></a>Cenários que podem utilizar a API do Gremlin
Eis alguns cenários em que o suporte de gráficos do Azure Cosmos DB pode ser utilizado:

* Redes sociais

 Ao combinar dados sobre os seus clientes e as respetivas interações com outras pessoas, pode desenvolver experiências personalizadas, prever o comportamento dos clientes ou ligar pessoas com interesses semelhantes. O Azure Cosmos DB pode ser utilizado para gerir redes sociais e registar os dados e preferências dos clientes.

* Motores de recomendações

 Este cenário é normalmente utilizado na indústria do retalho. Ao combinar informações sobre produtos, utilizadores e as respetivas interações, como compras, pesquisas ou classificações de produtos, pode criar recomendações personalizadas. A baixa latência, dimensionamento elástico e suporte de gráficos nativo do Azure Cosmos DB são ideais para modelar estas interações.

* Geoespacial

 Muitas aplicações de telecomunicações, logística e planeamento de viagens precisam de localizar um ponto de interesse numa área ou o caminho mais curto/ideal entre duas localizações. O Azure Cosmos DB é uma solução natural para estes problemas.

* Internet das Coisas

 Com a rede e ligações entre dispositivos da IoT modeladas como um gráfico, pode compreender melhor o estado dos seus dispositivos e recursos. Também pode saber como as alterações numa parte da rede podem potencialmente afetar outra parte.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o suporte de gráficos no Azure Cosmos DB, veja:

* Introdução ao [tutorial sobre gráficos do Azure Cosmos DB](create-graph-dotnet.md).
* Saiba como [consultar gráficos no Azure Cosmos DB com o Gremlin](gremlin-support.md).
