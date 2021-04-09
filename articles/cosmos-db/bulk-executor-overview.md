---
title: Descrição geral da biblioteca de executor em massa do Azure Cosmos DB
description: Realize operações a granel em Azure Cosmos DB através de APIs de importação a granel e atualização a granel oferecidas pela biblioteca executora a granel.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 211fc85f97069fcf3251048a074d625e777f8e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100478"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Descrição geral da biblioteca de executor em massa do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
O Azure Cosmos DB é um serviço de base de dados rápido, flexível e distribuído globalmente concebido para aumentar horizontalmente de forma elástica de forma a suportar: 

* O débito de leitura e escrita elevado (milhões de operações por segundo).  
* O armazenamento de grandes volumes (centenas de terabytes ou até mesmo mais) de dados transacionais e operacionais, com uma latência de milissegundos previsível.  

A biblioteca de executor em massa ajuda-o a tirar partido deste armazenamento e débito massivo. A biblioteca de executor em massa permite-lhe realizar operações em massa no Azure Cosmos DB através de APIs de importação em massa e de atualização em massa. Pode ler mais sobre as funcionalidades da biblioteca de executor em massa nas seções a seguir. 

> [!NOTE] 
> Atualmente, a biblioteca de executores a granel suporta operações de importação e atualização e esta biblioteca é suportada apenas por contas API DB SQL E Gremlin.

> [!IMPORTANT]
> A biblioteca de executor a granel não é suportada atualmente em contas [sem servidor.](serverless.md) Em .NET, recomenda-se a utilização do suporte a [granel](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) disponível na versão V3 do SDK.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Principais características da biblioteca do executor a granel  
 
* Reduz significativamente os recursos de computação do lado do cliente necessários para saturar a produção atribuída a um contentor. Uma aplicação de linha única que escreve dados utilizando a API de importação a granel obtém 10 vezes maior produção de escrita quando comparada com uma aplicação multi-roscada que escreve dados em paralelo enquanto satura o CPU da máquina cliente.  

* Ele abstrate as tarefas tediosas de escrever lógica de aplicação para lidar com a limitação de taxas de pedido, solicitar intervalos de tempo e outras exceções transitórias, manuseando-as eficientemente dentro da biblioteca.  

* Fornece um mecanismo simplificado para que as aplicações que executam operações a granel se escalonem. Um único executor a granel em execução num Azure VM pode consumir mais de 500K RU/s e você pode obter uma taxa de produção mais alta adicionando instâncias adicionais em VMs de cliente individual.  
 
* Pode importar em massa mais do que um terabyte de dados dentro de uma hora, utilizando uma arquitetura de escala.  

* Pode atualizar em massa os dados existentes em contentores Azure Cosmos como patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Como funciona o executor a granel? 

Quando uma operação a granel para importar ou atualizar documentos é desencadeada com um lote de entidades, eles são inicialmente baralhados em baldes correspondentes à sua gama de chaves de partição Azure Cosmos DB. Dentro de cada balde que corresponde a uma gama de chaves de partição, são divididos em mini-lotes e cada mini-lote atua como uma carga útil que é comprometida no lado do servidor. A biblioteca de executor a granel construiu em otimizações para a execução simultânea destes mini-lotes dentro e em todas as gamas de chaves de partição. A imagem seguinte ilustra como o executor a granel em lotes de dados em diferentes teclas de partição:  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="Arquitetura executora a granel" :::

A biblioteca de executores a granel assegura-se de utilizar máximamente a produção atribuída a uma coleção. Utiliza um [mecanismo de controlo de congestionamento ao estilo AIMD](https://tools.ietf.org/html/rfc5681) para cada gama de chaves de partição DB Azure Cosmos para lidar eficazmente com a limitação de tarifas e intervalos de tempo. 

## <a name="next-steps"></a>Passos Seguintes 
  
* Saiba mais experimentando as aplicações de amostra que consomem a biblioteca de executor a granel em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md).  
* Consulte as informações do executor a granel SDK e as notas de lançamento em [.NET](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* A biblioteca de executor a granel está integrada no conector Cosmos DB Spark, para saber mais, ver artigo [do conector Azure Cosmos DB Spark.](spark-connector.md)  
* A biblioteca de executores a granel também está integrada numa nova versão do [conector DB Azure Cosmos](../data-factory/connector-azure-cosmos-db.md) para a Azure Data Factory copiar dados.
