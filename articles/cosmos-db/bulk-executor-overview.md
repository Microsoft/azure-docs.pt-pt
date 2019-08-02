---
title: Importar e atualizar dados em massa no Azure Cosmos DB usando a biblioteca de executores em massa
description: Execute operações em massa em Azure Cosmos DB por meio de APIs de importação em massa e de atualização em massa oferecidas pela biblioteca de executor em massa.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 1716bd64286f1882b9fc224712d227967d78058a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637786"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Visão geral do Azure Cosmos DB biblioteca de executores em massa
 
O Azure Cosmos DB é um serviço de base de dados rápido, flexível e distribuído globalmente concebido para ampliar elasticamente de forma a suportar: 

* O débito de leitura e escrita elevado (milhões de operações por segundo).  
* O armazenamento de grandes volumes (centenas de terabytes ou até mesmo mais) de dados transacionais e operacionais, com uma latência de milissegundos previsível.  

A biblioteca de executor em massa ajuda-o a tirar partido deste armazenamento e débito massivo. A biblioteca de executor em massa permite-lhe realizar operações em massa no Azure Cosmos DB através de APIs de importação em massa e de atualização em massa. Pode ler mais sobre as funcionalidades da biblioteca de executor em massa nas seções a seguir. 

> [!NOTE] 
> Atualmente, a biblioteca de executores em massa dá suporte a operações de importação e atualização e essa biblioteca tem suporte somente por Azure Cosmos DB de contas de API do SQL e do Gremlin.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Principais recursos da biblioteca de executores em massa  
 
* Ele reduz significativamente os recursos de computação do lado do cliente necessários para saturar a taxa de transferência alocada para um contêiner. Um aplicativo de thread único que grava dados usando a API de importação em massa atinge 10 vezes mais produtividade de gravação quando comparado a um aplicativo multi-threaded que grava dados em paralelo enquanto saturação a CPU do computador cliente.  

* Ela abstrai as tarefas entediantes de escrever lógica do aplicativo para lidar com a limitação de taxa de solicitação, tempos limite de solicitação e outras exceções transitórias, tratando-as com eficiência na biblioteca.  

* Ele fornece um mecanismo simplificado para aplicativos que executam operações em massa para escalar horizontalmente. Uma única instância de executor em massa em execução em uma VM do Azure pode consumir mais de 500 mil RU/s e você pode obter uma taxa de transferência mais alta adicionando mais instâncias em VMs de cliente individuais.  
 
* Ele pode importar em massa mais de um terabyte de dados em uma hora usando uma arquitetura de expansão.  

* Ele pode atualizar dados existentes em massa em contêineres de Azure Cosmos DB como patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Como funciona o executor em massa? 

Quando uma operação em massa para importar ou atualizar documentos é disparada com um lote de entidades, elas são inicialmente embaralhadas em buckets correspondentes ao seu Azure Cosmos DB intervalo de chaves de partição. Dentro de cada bucket que corresponde a um intervalo de chaves de partição, eles são divididos em mini-lotes e cada mini-lote atua como uma carga confirmada no lado do servidor. A biblioteca de executores em massa tem otimizações internas para a execução simultânea desses mini-lotes em e entre intervalos de chaves de partição. A imagem a seguir ilustra como o executor em massa agrupa dados em chaves de partição diferentes:  

![Arquitetura de executor em massa](./media/bulk-executor-overview/bulk-executor-architecture.png)

A biblioteca de executores em massa garante o uso máximo da taxa de transferência alocada para uma coleção. Ele usa um [mecanismo de controle de congestionamento de estilo AIMD](https://tools.ietf.org/html/rfc5681) para cada intervalo de chave de partição Azure Cosmos DB para manipular com eficiência a limitação de taxa e os tempos limite. 

## <a name="next-steps"></a>Próximos Passos 
  
* Saiba mais experimentando os aplicativos de exemplo que consomem a biblioteca de executores em massa no [.net](bulk-executor-dot-net.md) e no [Java](bulk-executor-java.md).  
* Confira as informações do SDK de executor em massa e notas de versão em [.net](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* A biblioteca de executores em massa é integrada ao conector do Cosmos DB Spark, para saber mais, consulte o artigo [Azure Cosmos DB conector do Spark](spark-connector.md) .  
* A biblioteca de executores em massa também é integrada a uma nova versão do [conector do Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) para Azure data Factory copiar dados.
