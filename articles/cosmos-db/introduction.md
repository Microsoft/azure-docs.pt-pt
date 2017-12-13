---
title: "Introdução ao Azure Cosmos DB | Microsoft Docs"
description: "Saiba mais sobre o Azure Cosmos DB. Esta base de dados de distribuição global com vários modelos foi concebida para baixa latência, escalabilidade elástica e elevada disponibilidade."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: f34790ad670b488159e945be9bf4ba378cc5e94a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

O Azure Cosmos DB é uma base de dados com vários modelos distribuída globalmente. Com o clique de um botão, o Azure Cosmos DB permite-lhe dimensionar de forma elástica e independente o débito e o armazenamento em qualquer número de regiões geográficas do Azure. Oferece débito, latência, disponibilidade e garante a consistência com abrangentes [contratos de nível de serviço](https://aka.ms/acdbsla) (SLAs), que nenhum outro serviço da base de dados oferece. Pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.

![O Azure Cosmos DB é um serviço de base de dados de distribuição global da Microsoft com aumento horizontal elástico, baixa latência garantida, cinco modelos de consistência e SLAs abrangentes garantidos](./media/introduction/azure-cosmos-db.png)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

## <a name="key-capabilities"></a>Principais capacidades
Como um serviço de base de dados de distribuição global, o Azure Cosmos DB proporciona as seguintes capacidades para ajudar a criar aplicações dimensionáveis e com elevada capacidade de resposta:

* **Distribuição global chave na mão**
    * Pode [distribuir os seus dados](distribute-data-globally.md) para qualquer número de [regiões do Azure](https://azure.microsoft.com/regions/) com um mero [clique num botão](tutorial-global-distribution-documentdb.md). Desta forma, pode colocar os dados onde os seus utilizadores estão, garantindo a menor latência possível aos seus clientes. 
    * Com as APIs multi-homing do Azure Cosmos DB, a aplicação sabe sempre qual é a região mais próxima e envia pedidos para o datacenter mais perto. Tudo isto pode ser feito sem alterações de configuração. Defina a sua região de escrita e tantas regiões de leitura quantas pretender e o resto é feito automaticamente.

* **Vários modelos de dados e APIs populares para aceder e consultar os dados**
    * O modelo de dados baseado em sequência de registo atom (ARS) sob o qual o Azure Cosmos DB é criado suporta nativamente vários modelos de dados, incluindo, entre outros, modelos de documentos, gráficos, chaves-valores, tabela e dados em colunas.
    * As APIs para os modelos de dados seguintes são suportadas com os SDKs disponíveis em várias linguagens:
        * [API do DocumentDB](documentdb-introduction.md): um motor de base de dados JSON sem esquemas com capacidades de consultas SQL.
        * [API do MongoDB](mongodb-introduction.md): um serviço de base de dados do MongoDB desenvolvido com base no Cosmos DB. É compatível com bibliotecas, controladores, ferramentas e aplicações existentes do MongoDB.
        * [API de tabela](table-introduction.md): um serviço de base de dados de chave/valor criado para oferecer capacidades premium para aplicações de armazenamento de Tabelas do Azure.
        * [Graph API (Gremlin)](graph-introduction.md): um serviço de base de dados do gráfico criado com base na [especificação do Apache TinkerPop](http://tinkerpop.apache.org/).
        * [API de Cassandra](cassandra-introduction.md): um arquivo de chave/valor criado com base na implementação do [Apache Cassandra](https://cassandra.apache.org/). 
        * Modelos de dados adicionais disponíveis em breve!

* **Dimensionar de forma elástica o débito e o armazenamento a pedido, em todo o mundo**
    * Dimensione facilmente o débito das bases de dados a uma granularidade de [por segundo](request-units.md) e altere-o sempre que quiser. 
    * Dimensione o tamanho do armazenamento de modo [transparente e automático](partition-data.md) para satisfazer os seus requisitos de tamanho de forma permanente.

* **Criar aplicações com elevada capacidade de resposta e críticas para a missão**
    * O Azure Cosmos DB garante baixa latência ponto a ponto no percentil 99 aos seus clientes. 
    * Para um item de 1 KB típico, o Cosmos DB garante a latência ponto a ponto das leituras abaixo dos 10 ms e das escritas indexadas abaixo dos 15 ms no percentil 99, dentro da mesma região do Azure. As latências medianas são significativamente inferiores (abaixo dos 5 ms).

* **Garantia de disponibilidade “always on”**
    * SLA de 99,99% disponibilidade para todas as contas de região única e para todas as contas de várias regiões com consistência flexível e 99,999% de disponibilidade de leitura em todas as contas de bases de dados de várias regiões.
    * Para maior disponibilidade, implemente em qualquer número de [regiões do Azure](https://azure.microsoft.com/regions).
    * [Simule uma falha](regional-failover.md) de uma ou mais regiões com garantia de zero perda de dados. 

* **Escrever aplicações de distribuição global, da forma correta**
    * Cinco [modelos de consistência](consistency-levels.md) oferecem um espetro que vai desde consistência forte semelhante ao SQL até consistência eventual semelhante ao NoSQL e todas as outras consistências entre estas duas. 
  
* **Garantia de devolução do dinheiro**
    * Os seus dados chegam rápido ou recebe o seu dinheiro de volta. 
    * [Contratos de nível de serviço](https://aka.ms/acdbsla) para disponibilidade, latência, débito e consistência. 

* **Sem gestão de esquema/índices de bases de dados**
    * Deixe de ter de se preocupar em manter o esquema e os índices das bases de dados em sincronização com o esquema da aplicação. Não utilizamos esquemas. 
    * O motor de base de dados do Azure Cosmos DB é totalmente sem esquema. Indexa automaticamente todos os dados que ingere sem necessidade de qualquer esquema ou índice e serve consultas muito rápidas. 

* **Baixo custo de propriedade**
    * Cinco a dez vezes [mais económica](https://aka.ms/cosmos-db-tco-paper) que uma solução não gerida.
    * Três vezes mais barata que a DynamoDB.

## <a name="capability-comparison"></a>Comparação das capacidades

O Azure Cosmos DB fornece as melhores capacidades das bases de dados relacionais e não relacionais.

| Capacidades | Bases de dados relacionais   | Bases de dados não relacionais (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribuição global | Não | Não | Sim, distribuição chave na mão em mais de 30 regiões, com APIs multi-homing|
| Dimensionamento horizontal | Não | Sim | Sim, pode dimensionar o armazenamento e o débito de forma independente | 
| Garantias de latência | Não | Sim | Sim, 99% de leituras em < 10 ms e de escritas em < 15 ms | 
| Elevada disponibilidade | Não | Sim | Sim, o Cosmos DB está sempre ativado (“always on”), tem compromissos PACELC e disponibiliza opções de ativação pós-falha automáticas e manuais|
| Modelo de dados + API | Relacional + SQL | Vários modelos e API OSS | Vários modelos + SQL + API OSS (mais em breve) |
| SLAs | Sim | Não | Sim, SLAs abrangentes para latência, débito, consistência e disponibilidade |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que tiram partido do Azure Cosmos DB

Qualquer [aplicação Web, móvel, de jogos e de IoT](use-cases.md) que tenha de lidar com quantidades gigantescas de leituras e escritas numa escala [global](distribute-data-globally.md) com tempos de resposta curtos para os mais variados dados tira partido da disponibilidade [garantida](https://azure.microsoft.com/support/legal/sla/cosmos-db/), do elevado débito, da baixa latência e da consistência ajustável do Azure Cosmos DB. Saiba mais sobre como o CosmosDB pode ser aplicado no [IoT e telemático](use-cases.md#iot-and-telematics), [Revenda e marketing](use-cases.md#retail-and-marketing), [Jogos](use-cases.md#gaming) e [aplicações móveis e Web](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Passos seguintes
Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API DocumentDB do Azure Cosmos DB](create-documentdb-dotnet.md)
* [Introdução à API MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à Graph API do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
