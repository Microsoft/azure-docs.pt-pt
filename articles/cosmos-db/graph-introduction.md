---
title: Introdução à Azure Cosmos DB Gremlin API
description: Saiba como pode utilizar o Azure Cosmos DB para armazenar, consultar e percorrer gráficos enormes com baixa latência através da linguagem de consulta de gráficos Gremlin do Apache TinkerPop.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/10/2020
ms.author: chrande
ms.openlocfilehash: d0bd94037a75db8d69cfd44820a80ae8b403c9ea
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357084"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Introdução à API de Gremlin em Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Azure Cosmos DB](introduction.md)   é o serviço de base de dados multi-modelo distribuído globalmente pela Microsoft para aplicações críticas de missão. É uma base de dados multi-modelo e suporta documentos, modelos de dados de valor-chave, gráfico e coluna-família. "A Azure Cosmos DB fornece um serviço de base de dados de gráficos através da API gremlin num serviço de base de dados totalmente gerido projetado para qualquer escala.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Arquitetura de gráficos do Azure Cosmos DB" border="false":::

Este artigo fornece uma visão geral da API AZure Cosmos DB Gremlin e explica como usá-los para armazenar gráficos maciços com biliões de vértices e bordas. Pode consultar os gráficos com latência milissegundo e evoluir facilmente a estrutura do gráfico. A API gremlin da Azure Cosmos DB é construída com base no [Apache TinkerPop,](https://tinkerpop.apache.org)uma estrutura de computação de gráficos. A API gremlin em Azure Cosmos DB usa a linguagem de consulta Gremlin.

A API gremlin da Azure Cosmos DB combina o poder dos algoritmos de base de dados de gráficos com infraestruturas altamente escaláveis e geridas para fornecer uma solução única e flexível para os problemas de dados mais comuns associados à falta de flexibilidade e abordagens relacionais.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Características da API gremlin da Azure Cosmos DB
 
O Azure Cosmos DB é uma base de dados de grafos completamente gerida que oferece distribuição global, dimensionamento elástico do débito e armazenamento, consulta e indexação automática, níveis de consistência ajustáveis e suporte para o padrão TinkerPop.

Seguem-se as características diferenciadas que a AZure Cosmos DB Gremlin API oferece:

* **Débito e armazenamento dimensionável de forma elástica**

  Os gráficos no mundo real precisam de ser dimensionados para além da capacidade de um único servidor. A Azure Cosmos DB suporta bases de dados de gráficos horizontalmente escaláveis que podem ter um tamanho praticamente ilimitado em termos de armazenamento e produção a provisionada. À medida que a escala da base de dados de gráficos aumenta, os dados serão automaticamente distribuídos através da [partição de gráficos](./graph-partitioning.md).

* **Replicação de várias regiões**

  O Azure Cosmos DB pode replicar automaticamente os seus dados gráficos em qualquer região do Azure em todo o mundo. A replicação global simplifica o desenvolvimento de aplicações que requerem acesso global aos dados. Além de minimizar a latência de leitura e escrita em qualquer parte do mundo, a Azure Cosmos DB fornece um mecanismo de failover regional automático que pode garantir a continuidade da sua aplicação em caso raro de interrupção de serviço numa região.

* **Consultas rápidas e traversais com o padrão de consulta de gráfico mais amplamente adotado**

  Guarde vértices e arestas heterogéneas e questione-os através de uma sintaxe gremlin familiar. Gremlin é uma linguagem de consulta funcional e imperativa que fornece uma interface rica para implementar algoritmos de gráficos comuns.
  
  Azure Cosmos DB permite consultas e travessias ricas em tempo real sem a necessidade de especificar sugestões de esquemas, índices secundários ou vistas. Saiba mais em [Query graphs by using Gremlin](gremlin-support.md) (Consultar gráficos com o Gremlin).

* **Base de dados de gráficos totalmente gerida**

  O Azure Cosmos DB elimina a necessidade de gerir recursos de máquinas e bases de dados. A maioria das plataformas de base de dados de gráficos existentes estão ligadas às limitações da sua infraestrutura e muitas vezes exigem um elevado grau de manutenção para garantir o seu funcionamento. 
  
  Como um serviço totalmente gerido, cosmos DB remove a necessidade de gerir máquinas virtuais, atualizar software de tempo de execução, gerir o fragmento ou replicação, ou lidar com atualizações complexas de nível de dados. São criadas cópias de segurança automáticas de todos os gráficos e estes são protegidos contra falhas regionais. Isto permite que os desenvolvedores se concentrem na entrega do valor da aplicação em vez de operarem e gerirem as suas bases de dados de gráficos. 

* **Indexação automática**

  Por padrão, a Azure Cosmos DB indexa automaticamente todas as propriedades dentro dos nós (também chamados de vértices) e bordas no gráfico e não espera ou requer qualquer esquema ou criação de índices secundários. Saiba mais sobre [a indexação em Azure Cosmos DB](/azure/cosmos-db/index-overview).

* **Compatibilidade com o Apache TinkerPop**

  A Azure Cosmos DB suporta o [padrão Apache TinkerPop de código aberto](https://tinkerpop.apache.org/). O padrão Tinkerpop tem um amplo ecossistema de aplicações e bibliotecas que podem ser facilmente integrados com a API Gremlin da Azure Cosmos DB.

* **Níveis de consistência ajustáveis**

  A Azure Cosmos DB fornece cinco níveis de consistência bem definidos para alcançar a troca certa entre consistência e desempenho para a sua aplicação. Para consultas e operações de leitura, o Azure Cosmos DB oferece cinco níveis de consistência distintos: forte, consistência vinculada, sessão, prefixo de consistência e eventual. Estes níveis de consistência granulares e bem definidos permitem-lhe atingir um equilíbrio eficaz entre a consistência, a disponibilidade e a latência. Saiba mais em [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB).

## <a name="scenarios-that-use-gremlin-api"></a>Cenários que usam a API gremlin

Aqui estão alguns cenários onde o suporte gráfico de Azure Cosmos DB pode ser útil:

* **Redes sociais/Cliente 365**

  Ao combinar dados sobre os seus clientes e as respetivas interações com outras pessoas, pode desenvolver experiências personalizadas, prever o comportamento dos clientes ou ligar pessoas com interesses semelhantes. O Azure Cosmos DB pode ser utilizado para gerir redes sociais e registar os dados e preferências dos clientes.

* **Motores de recomendações**

  Este cenário é normalmente utilizado na indústria do retalho. Ao combinar informações sobre produtos, utilizadores e as respetivas interações, como compras, pesquisas ou classificações de produtos, pode criar recomendações personalizadas. A baixa latência, a escala elástica e o suporte de gráficos nativos da Azure Cosmos DB são ideais para estes cenários.

* **Geoespacial**

  Muitas aplicações de telecomunicações, logística e planeamento de viagens precisam de localizar um ponto de interesse numa área ou o caminho mais curto/ideal entre duas localizações. O Azure Cosmos DB é uma solução natural para estes problemas.

* **Internet das Coisas**

  Com a rede e ligações entre dispositivos da IoT modeladas como um gráfico, pode compreender melhor o estado dos seus dispositivos e recursos. Também pode saber como as alterações numa parte da rede podem potencialmente afetar outra parte.

## <a name="introduction-to-graph-databases"></a>Introdução às bases de dados de gráficos

Os dados, tal como são apresentados no mundo real, estão ligados naturalmente. A modelação de dados tradicionais centra-se na definição separada das entidades e na computação das suas relações em tempo de execução. Embora este modelo tenha as suas vantagens, os dados altamente conectados podem ser desafiantes para gerir sob os seus constrangimentos.  

Uma abordagem da base de dados de gráficos baseia-se em relações persistentes na camada de armazenamento, o que leva a operações de recuperação de gráficos altamente eficientes. A AZure Cosmos DB's Gremlin API suporta o [modelo de gráfico de propriedade.](https://tinkerpop.apache.org/docs/current/reference/#intro)

### <a name="property-graph-objects"></a>Objetos de gráfico de propriedade

Um [gráfico de](http://mathworld.wolfram.com/Graph.html) propriedade é uma estrutura composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [bordas.](http://mathworld.wolfram.com/GraphEdge.html) Ambos os objetos podem ter um número arbitrário de pares de valores-chave como propriedades. 

* **Vértices/nós** - Os vértices denotam entidades discretas, como uma pessoa, um lugar ou um evento.

* **Bordas/relacionamentos** - Bordas denotam relações entre vértices. Por exemplo, uma pessoa pode conhecer outra pessoa, participar num evento e ter estado numa localização recentemente.

* **Propriedades** - As propriedades expressam informações sobre os vértices e margens. Pode haver qualquer número de propriedades em vértices ou bordas, e podem ser usadas para descrever e filtrar os objetos numa consulta. As propriedades exemplo incluem um vértice que tem nome e idade, ou uma borda, que pode ter um carimbo de tempo e/ou um peso.

* **Etiqueta** - Uma etiqueta é um nome ou o identificador de um vértice ou de uma borda. As etiquetas podem agrupar vários vértices ou arestas de modo a que todos os vértices/arestas de um grupo tenham uma determinada etiqueta. Por exemplo, um gráfico pode ter vários vértices do tipo de etiqueta "pessoa".

As bases de dados de gráficos são frequentemente incluídas na categoria NoSQL ou na categoria de base de dados não relacional, uma vez que não existe dependência de um esquema ou de um modelo de dados constrangido. Esta falta de esquema permite modelar e armazenar estruturas conectadas de forma natural e eficiente.

### <a name="graph-database-by-example"></a>Base de dados de gráficos por exemplo

Vamos utilizar um gráfico de exemplo para compreender como as consultas podem ser expressadas no Gremlin. A imagem seguinte apresenta uma aplicação empresarial que gere dados sobre os utilizadores, interesses e dispositivos sob a forma de um gráfico.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Base de dados de exemplo a mostrar pessoas, dispositivos e interesses" border="false"::: 

Este gráfico tem os seguintes tipos *de vértice* (estes também são chamados de "rótulo" em Gremlin):

* **People** : O gráfico tem três pessoas, Robin, Thomas e Ben
* **Interesses** : Os seus interesses, neste exemplo, o jogo de futebol
* **Dispositivos** : Os dispositivos que as pessoas usam
* **Sistemas operativos** : Os sistemas operativos em que os dispositivos funcionam
* **Local** : Os locais a partir dos quais os dispositivos são acedidos

Representamos as relações entre estas entidades através dos seguintes tipos *de borda:*

* **Sabe:** Por exemplo, "Thomas conhece Robin"
* **Interessado** : Representar os interesses das pessoas no nosso gráfico, por exemplo, "Ben está interessado no futebol"
* **RunsOS** : Laptop executa o Windows OS
* **Utilizações:** Para representar o dispositivo que uma pessoa utiliza. Por exemplo, a Robin utiliza um telemóvel Motorola com o número de série 77
* **Local** : Para representar o local a partir do qual os dispositivos são acedidos

A Consola Gremlin é um terminal interativo oferecido pelo Apache TinkerPop e este terminal é usado para interagir com os dados do gráfico. Para saber mais, consulte o quickstart doc sobre [como usar a consola Gremlin](create-graph-gremlin-console.md). Também pode efetuar estas operações com os controladores do Gremlin na plataforma que preferir (Java, Node.js, Python ou .NET). Os exemplos a seguir mostram como executar consultas contra estes dados gráficos utilizando a Consola Gremlin.

Em primeiro lugar, vamos ver o CRUD. A seguinte instrução do Gremlin insere o vértice "Thomas" no gráfico:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Em seguida, a seguinte instrução do Gremlin insere um limite "knows" (conhece) entre o Thomas e a Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

A consulta seguinte devolve os vértices "person" (pessoa) por ordem descendente dos nomes próprios:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Os gráficos são bastante úteis quando tem de responder a perguntas como "What operating systems do friends of Thomas use?" (Que sistemas operativos utilizam os amigos do Thomas?). Você pode executar este Gremlin traversal para obter essa informação a partir do gráfico:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o suporte de gráficos no Azure Cosmos DB, veja:

* Introdução ao [tutorial sobre gráficos do Azure Cosmos DB](create-graph-dotnet.md).
* Saiba como [consultar gráficos no Azure Cosmos DB com o Gremlin](gremlin-support.md).