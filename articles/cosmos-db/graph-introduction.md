---
title: Introdução à Azure Cosmos DB Gremlin API
description: Saiba como pode utilizar o Azure Cosmos DB para armazenar, consultar e percorrer gráficos enormes com baixa latência através da linguagem de consulta de gráficos Gremlin do Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 0eb778d1fc2dca522bef675709b5241f214e2b86
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118122"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introdução ao Azure Cosmos DB: API do Gremlin

[Azure Cosmos DB](introduction.md)   é o serviço de base de dados multi-modelo distribuído globalmente pela Microsoft para aplicações críticas de missão. É uma base de dados multi-modelo e suporta documentos, modelos de dados de valor-chave, gráfico e coluna-família. A AZure Cosmos DB Gremlin API é usada para armazenar e operar com dados gráficos num serviço de base de dados totalmente gerido projetado para qualquer escala.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Arquitetura de gráficos do Azure Cosmos DB" border="false":::

Este artigo fornece uma descrição geral da API do Gremlin do Azure Cosmos DB e explica como pode utilizá-la para armazenar gráficos enormes com milhares de milhões de vértices e margens. Pode consultar os gráficos com latência milissegundo e evoluir facilmente a estrutura do gráfico. A API gremlin da Azure Cosmos DB baseia-se no padrão de base de dados de [gráficos Apache TinkerPop,](https://tinkerpop.apache.org)   e utiliza a linguagem de consulta Gremlin. 

A API gremlin da Azure Cosmos DB combina o poder dos algoritmos de base de dados de gráficos com infraestruturas altamente escaláveis e geridas para fornecer uma solução única e flexível para os problemas de dados mais comuns associados à falta de flexibilidade e abordagens relacionais. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funcionalidades da base de dados de gráficos do Azure Cosmos DB
 
O Azure Cosmos DB é uma base de dados de gráficos completamente gerida que oferece distribuição global, dimensionamento elástico do débito e armazenamento, consulta e indexação automática, níveis de consistência ajustáveis e suporte para o padrão TinkerPop. 

Seguem-se as características diferenciadas que a AZure Cosmos DB Gremlin API oferece:

* **Débito e armazenamento dimensionável de forma elástica**

  Os gráficos no mundo real precisam de ser dimensionados para além da capacidade de um único servidor. A Azure Cosmos DB suporta bases de dados de gráficos horizontalmente escaláveis que podem ter um tamanho praticamente ilimitado em termos de armazenamento e produção a provisionada. À medida que a escala da base de dados de gráficos aumenta, os dados serão automaticamente distribuídos através da [partição de gráficos](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replicação de várias regiões**

  O Azure Cosmos DB pode replicar automaticamente os seus dados gráficos em qualquer região do Azure em todo o mundo. A replicação global simplifica o desenvolvimento de aplicações que requerem acesso global aos dados. Além de minimizar a latência de leitura e escrita em qualquer parte do mundo, a Azure Cosmos DB fornece um mecanismo de failover regional automático que pode garantir a continuidade da sua aplicação em caso raro de interrupção de serviço numa região. 

* **Consultas rápidas e traversais com o padrão de consulta de gráfico mais amplamente adotado**

  Guarde vértices e arestas heterogéneas e questione-os através de uma sintaxe gremlin familiar. Gremlin é uma linguagem de consulta funcional e imperativa que fornece uma interface rica para implementar algoritmos de gráficos comuns. 
  
  Azure Cosmos DB permite consultas e travessias ricas em tempo real sem a necessidade de especificar sugestões de esquemas, índices secundários ou vistas. Saiba mais em [Query graphs by using Gremlin](gremlin-support.md) (Consultar gráficos com o Gremlin).

* **Base de dados de gráficos totalmente gerida**

  O Azure Cosmos DB elimina a necessidade de gerir recursos de máquinas e bases de dados. A maioria das plataformas de base de dados de gráficos existentes estão ligadas às limitações da sua infraestrutura e muitas vezes exigem um elevado grau de manutenção para garantir o seu funcionamento. 
  
  Como um serviço totalmente gerido, cosmos DB remove a necessidade de gerir máquinas virtuais, atualizar software de tempo de execução, gerir o fragmento ou replicação, ou lidar com atualizações complexas de nível de dados. São criadas cópias de segurança automáticas de todos os gráficos e estes são protegidos contra falhas regionais. Estas garantias permitem que os desenvolvedores se concentrem na entrega do valor da aplicação em vez de operarem e gerirem as suas bases de dados de gráficos. 

* **Indexação automática**

  Por predefinição, o Azure Cosmos DB indexa automaticamente todas as propriedades dos nós e margens do gráfico e não precisa de nenhum esquema ou criação de índices secundários. Saiba mais sobre [a indexação em Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Compatibilidade com o Apache TinkerPop**

  A Azure Cosmos DB suporta o [padrão Apache TinkerPop de código aberto](https://tinkerpop.apache.org/). O padrão Tinkerpop tem um amplo ecossistema de aplicações e bibliotecas que podem ser facilmente integrados com a API Gremlin da Azure Cosmos DB. 

* **Níveis de consistência ajustáveis**

  A Azure Cosmos DB fornece cinco níveis de consistência bem definidos para alcançar a troca certa entre consistência e desempenho para a sua aplicação. Para consultas e operações de leitura, o Azure Cosmos DB oferece cinco níveis de consistência distintos: forte, consistência vinculada, sessão, prefixo de consistência e eventual. Estes níveis de consistência granulares e bem definidos permitem-lhe atingir um equilíbrio eficaz entre a consistência, a disponibilidade e a latência. Saiba mais em [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB).

## <a name="scenarios-that-can-use-gremlin-api"></a>Cenários que podem utilizar a API do Gremlin
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

* **Vértices** - Vértices denotam entidades discretas, como uma pessoa, um lugar ou um evento.

* **Margens** - As margens denotam as relações entre vértices. Por exemplo, uma pessoa pode conhecer outra pessoa, participar num evento e ter estado numa localização recentemente. 

* **Propriedades** - As propriedades expressam informações sobre os vértices e margens. Pode haver qualquer número de propriedades em vértices ou bordas, e podem ser usadas para descrever e filtrar os objetos numa consulta. As propriedades exemplo incluem um vértice que tem nome e idade, ou uma borda, que pode ter um carimbo de tempo e/ou um peso. 

As bases de dados de gráficos são frequentemente incluídas na categoria NoSQL ou na categoria de base de dados não relacional, uma vez que não existe dependência de um esquema ou de um modelo de dados constrangido. Esta falta de esquema permite modelar e armazenar estruturas conectadas de forma natural e eficiente. 

### <a name="gremlin-by-example"></a>Exemplo do Gremlin
Vamos utilizar um gráfico de exemplo para compreender como as consultas podem ser expressadas no Gremlin. A imagem seguinte apresenta uma aplicação empresarial que gere dados sobre os utilizadores, interesses e dispositivos sob a forma de um gráfico.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Base de dados de exemplo a mostrar pessoas, dispositivos e interesses" border="false"::: 

Este gráfico tem os seguintes tipos *de vértice* (chamado "rótulo" em Gremlin):

- **People**: O gráfico tem três pessoas, Robin, Thomas e Ben
- **Interesses**: Os seus interesses, neste exemplo, o jogo de futebol
- **Dispositivos**: Os dispositivos que as pessoas usam
- **Sistemas operativos**: Os sistemas operativos em que os dispositivos funcionam

Representamos as relações entre estas entidades através dos seguintes tipos/rótulos de *borda:*

- **Sabe:** Por exemplo, "Thomas conhece Robin"
- **Interessado**: Representar os interesses das pessoas no nosso gráfico, por exemplo, "Ben está interessado no futebol"
- **RunsOS**: Laptop executa o Windows OS
- **Utilizações:** Para representar o dispositivo que uma pessoa utiliza. Por exemplo, a Robin utiliza um telemóvel Motorola com o número de série 77

Vamos executar algumas operações em relação a este gráfico com a [Consola do Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). Também pode efetuar estas operações com os controladores do Gremlin na plataforma que preferir (Java, Node.js, Python ou .NET).  Antes de vermos o que é suportado no Azure Cosmos DB, vamos ver alguns exemplos para se familiarizar com a sintaxe.

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

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o suporte de gráficos no Azure Cosmos DB, veja:

* Introdução ao [tutorial sobre gráficos do Azure Cosmos DB](create-graph-dotnet.md).
* Saiba como [consultar gráficos no Azure Cosmos DB com o Gremlin](gremlin-support.md).
