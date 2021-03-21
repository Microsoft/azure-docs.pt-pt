---
title: Problemas na grelha de eventos de resolução de problemas
description: Este artigo fornece diferentes formas de resolver problemas problemas da Grelha de Eventos Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720564"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Problemas na grelha de eventos do Azure
Este artigo fornece informações que o ajudam a resolver problemas com problemas na Grelha de Eventos do Azure. 

## <a name="diagnostic-logs"></a>Registos de diagnósticos
Ativar as definições de diagnóstico de tópicos ou domínios da Grade de Eventos para capturar e visualizar registos de falha de publicação e entrega. Para obter mais informações, consulte [os registos de diagnóstico.](enable-diagnostic-logs-topic.md)

## <a name="metrics"></a>Métricas
Pode ver métricas para tópicos e subscrições da Grade de Eventos e criar alertas sobre eles. Para obter mais informações, consulte as [métricas da Grade de Eventos.](monitor-event-delivery.md)

## <a name="alerts"></a>Alertas
Crie alertas sobre as métricas da Grelha de Eventos Azure e operações de registo de atividades. Para obter mais informações, consulte [alertas nas métricas da Grelha de Eventos e registos de atividades.](set-alerts.md)

## <a name="subscription-validation-issues"></a>Problemas de validação de assinaturas
Durante a criação de subscrição de eventos, poderá receber uma mensagem de erro que diz que a validação do ponto final fornecido falhou. Para resolver problemas de validação de subscrição, consulte [as validações de subscrição da Troubleshoot Event Grid](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problemas de conectividade de rede
Existem várias razões para as aplicações do cliente não conseguirem ligar-se a um tópico/domínio da Grade de Eventos. Os problemas de conectividade que experimenta podem ser permanentes ou transitórios. Para aprender a resolver estas questões, consulte [problemas de conectividade de resolução de problemas](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Códigos de erro
Se receber mensagens de erro com códigos de erro como 400, 409 e 403, consulte [os erros da Grelha de Eventos de Resolução de Problemas](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Rastreio distribuído (.NET)
A biblioteca Event Grid .NET suporta o rastreio de distribuição. Para aderir à [orientação da especificação CloudEvents](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) sobre a distribuição do rastreio, a biblioteca define `traceparent` as `tracestate` [extensõesTribus](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) de um `CloudEvent` quando o rastreio distribuído está ativado. Para saber mais sobre como permitir o rastreio distribuído na sua aplicação, dê uma olhada na [documentação de rastreio distribuída](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)do Azure SDK.

### <a name="sample"></a>Sample
Consulte a [amostra do contador de linha](/samples/azure/azure-sdk-for-net/line-counter/). Esta aplicação de amostras ilustra a utilização de clientes storage, Event Hubs e Event Grid juntamente com a integração ASP.NET Core, rastreio distribuído e serviços hospedados. Permite que os utilizadores carreguem um ficheiro para uma bolha, o que desencadeia um evento do Event Hubs que contém o nome do ficheiro. O Processador Event Hubs recebe o evento e, em seguida, a aplicação descarrega a bolha e conta o número de linhas no ficheiro. A aplicação apresenta um link para uma página que contém a contagem de linhas. Quando o link é clicado, um CloudEvent contendo o nome do ficheiro é publicado usando a Grade de Eventos.

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/) 
