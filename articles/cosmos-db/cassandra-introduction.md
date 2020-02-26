---
title: Introdução à API para Cassandra do Azure Cosmos DB
description: Saiba como pode usar o Azure Cosmos DB para "levantar e mudar" as aplicações existentes e construir novas aplicações utilizando os condutores cassandra e cQL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.openlocfilehash: 075b55b5a798924b55ef9d901e4d2e9ecfc9dc1e
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597580"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Introdução à API para Cassandra do Azure Cosmos DB

A API para Cassandra do Azure Cosmos DB pode ser utilizada como o arquivo de dados para as aplicações escritas para [Apache Cassandra](https://cassandra.apache.org). Isto significa que, ao utilizar os [controladores do Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) compatíveis com CQLv4, a sua aplicação do Cassandra existente pode agora comunicar com a API para Cassandra do Azure Cosmos DB. Em muitos casos, pode mudar de usar Apache Cassandra para usar a Cassandra API de Azure Cosmos DB, mudando apenas uma cadeia de ligação. 

A API para Cassandra permite-lhe interagir com os dados armazenados no Azure Cosmos DB ao utilizar a Linguagem de Consultas do Cassandra (CQL), ferramentas baseadas no Cassandra (como o cqlsh) e controladores de cliente do Cassandra que já conhece.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Qual é a vantagem de utilizar a API para Apache Cassandra do Azure Cosmos DB?

**Sem gestão de operações**: sendo um serviço cloud totalmente gerido, a API para Cassandra do Azure Cosmos DB elimina o overhead de gestão e monitorização de diversas definições em diferentes SOs, JVM e ficheiros yaml, bem como as respetivas interações. O Azure Cosmos DB proporciona a monitorização de débito, latência, armazenamento, disponibilidade e alertas configuráveis.

**Gestão de desempenho**: o Azure Cosmos DB fornece leituras e escritas de baixa latência garantidas no percentil 99, com suporte dos SLAs. Os utilizadores não têm de se preocupar com o overhead operacional para garantir um elevado desempenho e leituras e escritas de baixa latência. Isto significa que os utilizadores não precisam de lidar com o agendamento da compactação, a gestão de tombstones e a configuração de filtros de Bloom e réplicas manualmente. O Azure Cosmos DB elimina o overhead de gestão destes problemas e permite-lhe concentrar-se na lógica da aplicação.

**Capacidade de utilizar código e ferramentas existente**: o Azure Cosmos DB oferece compatibilidade ao nível do protocolo de transmissão com SDKs e ferramentas do Cassandra existentes. Esta compatibilidade assegura que pode utilizar o seu código base atual com a API para Cassandra do Azure Cosmos DB, com alterações triviais.

**Elasticidade de débito e armazenamento**: o Azure Cosmos DB proporciona um débito garantido em todas as regiões e pode dimensionar o débito aprovisionado com operações do portal do Azure, do PowerShell ou da CLI. Pode dimensionar com elasticidade o armazenamento e o débito das suas tabelas, conforme necessário, com um desempenho previsível.

**Distribuição e disponibilidade globais**: o Azure Cosmos DB proporciona a capacidade de distribuir dados globalmente em todas as regiões do Azure e disponibilizá-los localmente ao garantir um acesso a dados de baixa latência e uma elevada disponibilidade. O Azure Cosmos DB oferece uma elevada disponibilidade de 99,99% numa região e uma disponibilidade de leitura e escrita de 99,999% em várias regiões, sem overhead de operações. Saiba mais no artigo [Distribuir dados globalmente](distribute-data-globally.md). 

**Opções de consistência:** com o Azure Cosmos DB, pode escolher de entre cinco níveis de consistência bem definidos para obter os melhores compromissos entre consistência e desempenho. Estes níveis de consistência são forte, estagnação limitada, sessão, prefixo consistente e eventual. Estes níveis de consistência bem definidos, práticos e intuitivos permitem aos programadores fazer compromissos precisos entre a consistência, a disponibilidade e a latência. Saiba mais no artigo [Níveis de consistência](consistency-levels.md). 

**Nível empresarial**: o Azure Cosmos DB fornece [certificações de conformidade](https://www.microsoft.com/trustcenter) para garantir que os utilizadores podem utilizar a plataforma de forma segura. Também oferece encriptação inativa e em movimento, firewall de IPs e registos de auditorias para atividades do plano de controlo.

## <a name="next-steps"></a>Passos seguintes

* Pode começar a criar rapidamente as seguintes aplicações específicas de linguagens para criar e gerir dados da API para Cassandra:
  - [Aplicação Node.js](create-cassandra-nodejs.md)
  - [Aplicação .NET](create-cassandra-dotnet.md)
  - [Aplicação Python](create-cassandra-python.md)

* Começar a [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java.

* [Carregar dados de exemplo para a tabela da API para Cassandra](cassandra-api-load-data.md) ao utilizar uma aplicação Java.

* [Consultar dados da conta da API para Cassandra](cassandra-api-query-data.md) ao utilizar uma aplicação Java.

* Para saber mais sobre as funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB, veja o artigo [Suporte do Cassandra](cassandra-support.md).

* Leia as [Perguntas Mais Frequentes](faq.md#cassandra).
