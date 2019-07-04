---
title: Introdução ao Azure Cosmos DB a API do Gremlin
description: Saiba como pode utilizar o Azure Cosmos DB para armazenar, consultar e percorrer gráficos enormes com baixa latência através da linguagem de consulta de gráficos Gremlin do Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502806"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introdução ao Azure Cosmos DB: API do Gremlin

[O Azure Cosmos DB](introduction.md) é o serviço de base de dados de vários modelos distribuída globalmente da Microsoft para aplicativos de missão crítica. É uma base de dados com múltiplos modelo e oferece suporte a documentos, chave-valor, gráfico e modelos de dados em colunas. A API do Azure Cosmos DB Gremlin é utilizada para armazenar e trabalhar com dados de gráficos num ambiente de base de dados totalmente gerido em qualquer escala.  

![Arquitetura de gráficos do Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Este artigo fornece uma descrição geral da API do Gremlin do Azure Cosmos DB e explica como pode utilizá-la para armazenar gráficos enormes com milhares de milhões de vértices e margens. Pode consultar os gráficos com latência de milissegundos e evoluir facilmente a estrutura de gráfico. API do Gremlin do Azure Cosmos DB baseia-se sobre o [Apache TinkerPop](https://tinkerpop.apache.org) linguagem de consulta padrão da base de dados do gráfico e utiliza o Gremlin. 

API do Gremlin do DB Cosmos Azure combina a potência dos algoritmos de base de dados de gráfico com uma infraestrutura altamente dimensionável e gerida para fornecer uma solução de identificador exclusiva e flexível para problemas mais comuns de dados associadas com a falta de flexibilidade e abordagens relacionais. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funcionalidades da base de dados de gráficos do Azure Cosmos DB
 
O Azure Cosmos DB é uma base de dados de gráficos completamente gerida que oferece distribuição global, dimensionamento elástico do débito e armazenamento, consulta e indexação automática, níveis de consistência ajustáveis e suporte para o padrão TinkerPop. 

Seguem-se a recursos diferenciados que oferece a API de Gremlin do Azure Cosmos DB:

* **Débito dimensionável de forma elástica e armazenamento**

  Os gráficos no mundo real precisam de ser dimensionados para além da capacidade de um único servidor. Azure Cosmos DB suporta bases de dados de gráficos horizontalmente dimensionável que podem ter um tamanho praticamente ilimitado em termos de e o débito aprovisionado. À medida que aumenta a escala de base de dados do gráfico, os dados serão distribuídos automaticamente usando [criação de partições de gráfico](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replicação de várias regiões**

  O Azure Cosmos DB pode replicar automaticamente os dados do gráfico para qualquer região do Azure. Replicação simplifica o desenvolvimento de aplicativos que necessitam de acesso global aos dados. Além de minimizar a latência de leitura, o Azure Cosmos DB fornece um mecanismo de ativação pós-falha regional que pode garantir a continuidade do seu aplicativo no caso raro de uma interrupção do serviço numa região. 

* **Consultas rápidas e transversais com o padrão de consulta de gráfico mais adotados**

  Armazene margens e vértices heterogéneos e consulte esses documentos através de uma sintaxe Gremlin que já conhece. Gremlin é uma linguagem de consulta imperativo e funcional que fornece uma interface avançada para implementar algoritmos comuns de gráfico. 
  
  O Azure Cosmos DB permite consultas em tempo real e transversais sem a necessidade de especificar sugestões de esquema, índices secundários ou vistas. Saiba mais em [Query graphs by using Gremlin](gremlin-support.md) (Consultar gráficos com o Gremlin).

* **Base de dados totalmente gerido**

  O Azure Cosmos DB elimina a necessidade de gerir recursos de máquinas e bases de dados. A maioria das plataformas de base de dados do gráfico existentes estão vinculados às limitações de sua infra-estrutura e muitas vezes exigem um alto grau de manutenção para garantir que sua operação. 
  
  Como um serviço totalmente gerido do Microsoft Azure, não é necessário para gerir máquinas virtuais, atualização de software de tempo de execução, gerir a replicação ou fragmentação ou lidar com atualizações de camada de dados complexas. São criadas cópias de segurança automáticas de todos os gráficos e estes são protegidos contra falhas regionais. Essas garantias permitem que os desenvolvedores se concentram em oferecer valor de aplicação em vez de operando e gerenciando seus bancos de dados. 

* **Indexação automática**

  Por predefinição, o Azure Cosmos DB indexa automaticamente todas as propriedades dos nós e margens do gráfico e não precisa de nenhum esquema ou criação de índices secundários. Saiba mais sobre [indexação no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Compatibilidade com o Apache TinkerPop**

  Azure Cosmos DB suporta a [padrão do código-fonte aberto Apache TinkerPop](http://tinkerpop.apache.org/). O padrão de Tinkerpop tem um amplo ecossistema de aplicativos e bibliotecas que podem ser facilmente integradas com a API do Gremlin do Azure Cosmos DB. 

* **Níveis de consistência sincronizáveis**

  Selecione um dos cinco níveis de consistência bem definidos para alcançar um excelente equilíbrio entre a consistência e o desempenho. Para consultas e operações de leitura, o Azure Cosmos DB oferece cinco níveis de consistência distintos: forte, consistência vinculada, sessão, prefixo de consistência e eventual. Estes níveis de consistência granulares e bem definidos permitem-lhe atingir um equilíbrio eficaz entre a consistência, a disponibilidade e a latência. Saiba mais em [Tunable data consistency levels in Azure Cosmos DB](consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB).

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

## <a name="introduction-to-graph-databases"></a>Introdução às bases de dados do gráfico
Os dados, tal como são apresentados no mundo real, estão ligados naturalmente. Modelação de dados tradicionais se concentra na definição de entidades em separado e suas relações em tempo de execução de computação. Embora esse modelo tem suas vantagens, os dados altamente ligados podem ser difíceis de gerir em suas restrições.  

Uma abordagem de base de dados do gráfico se baseia em relações persistentes na camada de armazenamento em vez disso, que leva a operações de obtenção de gráfico altamente eficiente. API de Gremlin do Azure Cosmos DB suporta a [modelo de gráfico de propriedade](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Objetos de gráfico de propriedade

Uma propriedade [graph](http://mathworld.wolfram.com/Graph.html) é uma estrutura que é composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [margens](http://mathworld.wolfram.com/GraphEdge.html). Ambos os objetos podem ter um número arbitrário de pares chave-valor, como propriedades. 

* **Vértices** -vértices denotam entidades distintas, como uma pessoa, local ou um evento.

* **Margens** - As margens denotam as relações entre vértices. Por exemplo, uma pessoa pode conhecer outra pessoa, participar num evento e ter estado numa localização recentemente. 

* **Propriedades** - As propriedades expressam informações sobre os vértices e margens. É possível que qualquer número de propriedades nos vértices ou margens e eles podem ser usados para descrever e filtrar os objetos numa consulta. Propriedades de exemplo incluem um vértice que tem o nome e idade, ou um limite, o que pode ter um carimbo de data / hora e/ou um peso. 

Bases de dados do gráfico, muitas vezes, estão incluídos o NoSQL ou não-relacionais, categoria de base de dados, uma vez que não existe nenhuma dependência de um esquema ou o modelo de dados restrita. Essa falta de esquema permite a modelação e armazenar estruturas ligadas naturalmente e eficiente. 

### <a name="gremlin-by-example"></a>Exemplo do Gremlin
Vamos utilizar um gráfico de exemplo para compreender como as consultas podem ser expressadas no Gremlin. A imagem seguinte apresenta uma aplicação empresarial que gere dados sobre os utilizadores, interesses e dispositivos sob a forma de um gráfico.  

![Base de dados de exemplo a mostrar pessoas, dispositivos e interesses](./media/gremlin-support/sample-graph.png) 

Este gráfico tem os seguintes tipos de vértices (denominados "label" [etiqueta] no Gremlin):

- Pessoas: O gráfico tem três pessoas, Round Robin, Thomas e Ben
- Interesses: Seus interesses, neste exemplo, o jogo de futebol americano
- Dispositivos: Os dispositivos que as pessoas a utilização
- Sistemas operativos: Os sistemas operativos que os dispositivos a executar em

Representamos as relações entre estas entidades através das seguintes etiquetas/tipos de limites:

- Sabe: Por exemplo, "Thomas sabe Round Robin"
- Está interessado: Para representar os interesses das pessoas em nosso gráfico, por exemplo, "Ben está interessado em Football"
- RunsOS: Computador portátil executa o SO Windows
- Utiliza: Para representar que tipo de dispositivo utiliza uma pessoa. Por exemplo, a Robin utiliza um telemóvel Motorola com o número de série 77

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

Os gráficos são bastante úteis quando tem de responder a perguntas como "What operating systems do friends of Thomas use?" (Que sistemas operativos utilizam os amigos do Thomas?). Pode executar esta passagem de Gremlin para obter essa informação do gráfico:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Agora vamos ver o que o Azure Cosmos DB disponibiliza aos programadores do Gremlin.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o suporte de gráficos no Azure Cosmos DB, veja:

* Introdução ao [tutorial sobre gráficos do Azure Cosmos DB](create-graph-dotnet.md).
* Saiba como [consultar gráficos no Azure Cosmos DB com o Gremlin](gremlin-support.md).
