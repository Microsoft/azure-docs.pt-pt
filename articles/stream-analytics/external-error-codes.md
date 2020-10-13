---
title: Códigos de erro externos - Azure Stream Analytics
description: Resolução de problemas Problemas Azure Stream Analytics problemas com códigos de erro externos.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: f963f4c3538146b7f9929960092a18294659be5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045251"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Códigos de erro externos Azure Stream Analytics

Pode utilizar registos de atividades e registos de recursos para ajudar a depurar comportamentos inesperados do seu trabalho Azure Stream Analytics. Este artigo lista a descrição de cada código de erro externo. Erros externos são erros genéricos lançados por um serviço a montante ou a jusante que o Stream Analytics não distingue como um erro de dados, erro de configuração ou erro de disponibilidade externo.

## <a name="adapterinitializationerror"></a>AdaptadorInitializaçãoError

* **Causa**: Ocorreu um erro ao rubricar o adaptador.

## <a name="adapterfailedtowriteevents"></a>AdaptadorFailedToWriteEvents

* **Causa**: Ocorreu um erro ao escrever dados a um adaptador.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Causa**: Um erro HTTP foi devolvido das funções Azure.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Causa:** Stream Analytics não escreveu eventos para Azure Function.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Causa**: Existe um erro de redireccionamento ao desatado para funções Azure.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Causa:** Existe um erro do cliente a desausar para as Funções Azure.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Causa**: Existe um erro de saída do servidor para as Funções Azure.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Causa**: As funções de escrita para Azure falharam, uma vez que o pedido http excedeu o tempo limite. 
* **Recomendação**: Verifique os registos das funções Azure para eventuais atrasos.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Causa:** As compensações de entrada são inválidas. Isto pode ser devido a um fracasso.
* **Recomendação**: Reinicie o seu trabalho stream Analytics a partir da última hora de saída.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Causa:** Ocorreu um erro ao enviar dados para o Event Hub.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Causa**: Stream Analytics não conseguiu ligar-se a uma conta Cosmos DB após o número máximo de retrações.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Causa**: Stream Analytics não conseguiu consultar a base de dados e recolha do Cosmos DB após o número máximo de retrações.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Causa:** CosmosDB não pode criar um procedimento armazenado após várias retrações.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutoutrequestTimeout

* **Causa**: O procedimento de retenção retornado devolveu um erro. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Causa:** Stream Analytics não pode inicializar a saída da Base de Dados SQL.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Causa:** Stream Analytics não pode escrever eventos para a saída sql Database.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializaçãoError

* **Causa**: Ocorreu um erro ao rubricar uma saída de piscina Synapse SQL.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Causa**: Ocorreu um erro ao escrever a saída para uma piscina Sinapse SQL.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Resolução de problemas Saídas Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Resolução de problemas Azure Stream Análises](stream-analytics-troubleshoot-query.md)
* [Resolução de problemas Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
