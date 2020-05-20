---
title: Códigos de erro externos - Azure Stream Analytics
description: Problemas De saca-choques Azure Stream Analytics emite problemas com códigos de erro externos.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: fad0794a3628e6a89d1c2b2c80bb27d70937db95
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650044"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Códigos de erro externos azure Stream Analytics

Pode utilizar registos de atividade e registos de recursos para ajudar a desimpedir comportamentos inesperados do seu trabalho no Azure Stream Analytics. Este artigo lista a descrição de cada código de erro externo. Erros externos são erros genéricos lançados por um serviço a montante ou a jusante que o Stream Analytics não consegue distinguir como erro de dados, erro de configuração ou erro de disponibilidade externa.

## <a name="adapterinitializationerror"></a>AdaptadorInitializationError

* **Causa**: Ocorreu um erro ao inicializar o adaptador.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Causa**: Ocorreu um erro ao escrever dados a um adaptador.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Causa:** Um erro HTTP foi devolvido das funções Azure.

## <a name="azurefunctionfailedtosendmessage"></a>Mensagem de envio do AzureFunctionFailedToSend

* **Causa**: Stream Analytics não escreveu eventos para a Função Azure.

## <a name="azurefunctionredirecterror"></a>Erro de direção do AzureFunctionRe

* **Causa:** Existe um erro de redirecionamento ao ser colocado nas funções Azure.

## <a name="azurefunctionclienterror"></a>Erro AzureFunctionClientError

* **Causa:** Existe um erro do cliente na saída para as Funções Azure.

## <a name="azurefunctionservererror"></a>Erro do Servidor De Funções AzureFunction

* **Causa:** Existe um erro de erro do servidor a sair para as Funções Azure.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Causa**: A escrita para as funções Azure falhou, uma vez que o pedido de http excedeu o prazo. 
* **Recomendação**: Verifique os registos das suas Funções Azure para obter potenciais atrasos.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Causa:** As compensações de entrada são inválidas. Isto pode ser devido a uma falha.
* **Recomendação**: Reinicie o seu trabalho de Stream Analytics a partir do último período de saída.

## <a name="eventhubfailedtowriteevents"></a>EventoHubFailedToWriteEvents

* **Causa**: Ocorreu um erro ao enviar dados para o Event Hub.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Causa**: Stream Analytics não conseguiu ligar-se a uma conta Cosmos DB após o número máximo de repetições.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Causa**: Stream Analytics não conseguiu consultar a base de dados e a recolha da Cosmos DB após o número máximo de repetições.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Causa**: CosmosDB não pode criar um procedimento armazenado após várias tentativas.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Causa**: O procedimento mais acima armazenado devolveu um erro. 

## <a name="sqldatabaseoutputinitializationerror"></a>Erro de inicialização da sqlDatabaseOutput

* **Causa:** Stream Analytics não pode inicializar a saída de Base de Dados SQL.

## <a name="sqldatabaseoutputwriteerror"></a>Erro de escrita de base de dados de base de dados sql

* **Causa:** Stream Analytics não pode escrever eventos para a saída da Base de Dados SQL.

## <a name="sqldwoutputinitializationerror"></a>Erro de inicialização sQLDWOutput

* **Causa**: Ocorreu um erro ao inicializar uma saída de piscina Synapse SQL.

## <a name="sqldwoutputwriteerror"></a>Erro de escrita sQLDWOutput

* **Causa**: Ocorreu um erro ao escrever a saída para uma piscina Synapse SQL.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Saídas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Consultas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Troubleshoot Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
