---
title: Descrição geral da biblioteca de executor em massa do Azure Cosmos DB
description: Realizar operações a granel em Azure Cosmos DB através da importação a granel e da atualização a granel de APIs oferecidas pela biblioteca de executora a granel.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 9d335bcf6daf0b38e7a68ca2d40894dd64c93e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442159"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Descrição geral da biblioteca de executor em massa do Azure Cosmos DB
 
O Azure Cosmos DB é um serviço de base de dados rápido, flexível e distribuído globalmente concebido para ampliar elasticamente de forma a suportar: 

* O débito de leitura e escrita elevado (milhões de operações por segundo).  
* O armazenamento de grandes volumes (centenas de terabytes ou até mesmo mais) de dados transacionais e operacionais, com uma latência de milissegundos previsível.  

A biblioteca de executor em massa ajuda-o a tirar partido deste armazenamento e débito massivo. A biblioteca de executor em massa permite-lhe realizar operações em massa no Azure Cosmos DB através de APIs de importação em massa e de atualização em massa. Pode ler mais sobre as funcionalidades da biblioteca de executor em massa nas seções a seguir. 

> [!NOTE] 
> Atualmente, a biblioteca de executores a granel suporta operações de importação e atualização e esta biblioteca é apoiada apenas por contas API Da API e API da Azure Cosmos.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Principais características da biblioteca executor a granel  
 
* Reduz significativamente os recursos de computação do lado do cliente necessários para saturar a entrada atribuída a um contentor. Uma única aplicação roscada que escreve dados utilizando a API de importação a granel obtém 10 vezes maior potência escrita quando comparada com uma aplicação multi-threaded que escreve dados em paralelo enquanto satura o CPU da máquina cliente.  

* Resumia as tarefas aborrecidas de escrever a lógica da aplicação para lidar com a limitação de taxas de pedido, pedidos de tempo e outras exceções transitórias, manuseando-as de forma eficiente dentro da biblioteca.  

* Fornece um mecanismo simplificado para as aplicações que realizam operações a granel para se alargam. Uma única instância de executor a granel em execução num VM Azure pode consumir mais de 500K RU/s e você pode obter uma taxa de entrada mais alta adicionais em VMs de clientes individuais.  
 
* Pode importar mais do que um terabyte de dados dentro de uma hora, utilizando uma arquitetura de escala.  

* Pode atualizar em massa os dados existentes em contentores Azure Cosmos como patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Como funciona o executor a granel? 

Quando uma operação a granel para importar ou atualizar documentos é desencadeada com um lote de entidades, eles são inicialmente baralhados em baldes correspondentes à sua gama de divisórias Azure Cosmos DB. Dentro de cada balde que corresponde a uma gama de chaves de partição, são divididos em mini-lotes e cada mini-lote atua como uma carga útil que é cometida no lado do servidor. A biblioteca de executora a granel construiu em otimizações para a execução simultânea destes mini-lotes dentro e em todas as gamas de divisórias. A imagem seguinte ilustra como o executor a granel emlota dados em diferentes teclas de partição:  

![Arquitetura executora a granel](./media/bulk-executor-overview/bulk-executor-architecture.png)

A biblioteca de executor a granel garante utilizar maximamente a entrada atribuída a uma coleção. Utiliza um mecanismo de controlo de congestionamento ao [estilo AIMD](https://tools.ietf.org/html/rfc5681) para cada gama de divisórias Azure Cosmos DB para lidar eficientemente com a limitação da taxa e os intervalos de tempo. 

## <a name="next-steps"></a>Passos Seguintes 
  
* Saiba mais experimentando as aplicações de amostra que consomem a biblioteca de executora a granel em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md).  
* Confira as informações do executor a granel SDK e liberte as notas em [.NET](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* A biblioteca de executora a granel está integrada no conector Cosmos DB Spark, para saber mais, ver artigo de [conector Azure Cosmos DB Spark.](spark-connector.md)  
* A biblioteca de executora a granel também está integrada numa nova versão do [conector Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) para a Azure Data Factory para copiar dados.
