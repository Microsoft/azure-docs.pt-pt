---
title: Em massa importar e atualizar dados no Azure Cosmos DB com a biblioteca de executor em massa
description: Realizar operações em massa no Azure Cosmos DB através da importação em massa e APIs disponibilizadas pela biblioteca de executor em massa de atualização em massa.
services: cosmos-db
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.openlocfilehash: e3b77ce28e5c3a236abcd9a22e643428b53ed0a6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834813"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB em massa executor descrição geral da biblioteca
 
O Azure Cosmos DB é um serviço de base de dados rápida, flexível e distribuído globalmente que foi concebido para dimensionar horizontalmente para dar suporte a: 

* Grande de leitura e escrita de débito (milhões de operações por segundo).  
* Armazenar grandes volumes de (a centenas de terabytes ou até mesmo mais) dados transacionais e operacionais, com latência de milissegundo previsível.  

A biblioteca de executor em massa ajuda-o a tirar partido deste armazenamento e débito em massa. A biblioteca de executor em massa permite-lhe efetuar em massa APIs de atualização de operações no Azure Cosmos DB através da importação em massa e em massa. Pode ler mais sobre os recursos da biblioteca de executor em massa nas seções a seguir. 

> [!NOTE] 
> Atualmente, a biblioteca de executor em massa suporta a importação e operações de atualização e esta biblioteca é suportada pelo apenas para contas de API de SQL do Azure Cosmos DB e a API do Gremlin.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Principais recursos da biblioteca de executor em massa  
 
* Ela reduz consideravelmente os recursos de computação do lado do cliente necessários para saturar a taxa de transferência alocada a um contentor. Um único aplicativo com threads que escreve dados com que a API de importação em massa alcança 10 vezes maior débito de escrita em comparação com uma aplicação com múltiplos threads que grava dados em paralelo e saturar o cliente de CPU da máquina.  

* Ele abstrai tarefas entediantes escrever lógica de aplicação para processar a limitação de taxa de pedidos, tempos limite de pedido e outras exceções transitórias por tratá-los de modo eficiente na biblioteca.  

* Ele fornece um mecanismo simplificado para aplicativos que executam operações em massa aumentar horizontalmente. Uma instância de executor de em massa única em execução numa VM do Azure pode consumir mais de 500 mil RU/s e pode obter uma taxa de débito mais elevada, adicionando instâncias adicionais em VMs de clientes individuais.  
 
* Ele pode efetuar em massa importar mais do que um terabyte de dados dentro de uma hora ao utilizar uma arquitetura de escalamento horizontal.  

* Ele pode efetuar em massa de atualização de dados existentes nos contentores do Azure Cosmos DB como patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Como operar o Executor em massa? 

Quando uma operação em massa para importar ou atualizar documentos é acionada com um lote de entidades, eles são inicialmente combinadas de forma aleatória em buckets correspondente para o intervalo de chaves de partição do Azure Cosmos DB. Dentro de cada bucket que corresponde a um intervalo de chaves de partição, eles são divididos em lotes de mini e cada act de mini-batch como um payload confirmada no lado do servidor. A biblioteca de executor em massa desenvolveu-se em otimizações para a execução simultânea destes mini-lotes dentro e entre intervalos de chaves de partição. Imagem seguinte ilustra como executor de em massa lotes dados em chaves de partição diferentes:  

![Arquitetura de executor em massa](./media/bulk-executor-overview/bulk-executor-architecture.png)

A biblioteca de Executor em massa certifica-se de que maximally utilizam o débito alocado a uma coleção. Ele usa um [mecanismo de controlo de congestionamento de estilo AIMD](https://tools.ietf.org/html/rfc5681) para cada Azure Cosmos DB para manipular com eficiência a limitação de velocidade e tempos limite de intervalo da chave de partição. 

## <a name="next-steps"></a>Próximos Passos 
  
* Saiba mais experimentando os aplicativos de exemplo consumindo a biblioteca de Executor em massa na [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md).  
* Confira as massa executor SDK informações e notas de versão em [.NET](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* A biblioteca de Executor em massa está integrada ao conector do Spark do Cosmos DB, para obter mais informações, consulte [conector do Spark do Azure Cosmos DB](spark-connector.md) artigo.  
* A biblioteca de executor em massa também está integrada numa nova versão do [conector do Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) do Azure Data Factory copiar dados.
