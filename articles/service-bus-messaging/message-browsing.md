---
title: Azure Service Bus - navegação de mensagens
description: Navegue e espreite as mensagens Service Bus permite que um cliente da Azure Service Bus enumere todas as mensagens numa fila ou subscrição.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520127"
---
# <a name="message-browsing"></a>Navegação de mensagens
A navegação de mensagens, ou olhando, permite que um cliente do Service Bus enumerou todas as mensagens numa fila ou numa subscrição, para fins de diagnóstico e depuragem.

A operação Peek numa fila ou numa subscrição retorna no máximo o número de mensagens solicitadas. A tabela seguinte mostra os tipos de mensagens que são devolvidas pela operação Peek. 

| Tipo de mensagens | Incluído? | 
| ---------------- | ----- | 
| Mensagens ativas | Yes |
| Mensagens com letras mortas | No | 
| Mensagens bloqueadas | Yes |
| Mensagens caducadas |  Pode ser (antes de serem letra morta) |
| Mensagens agendadas | Sim para filas. Não para assinaturas |

## <a name="dead-lettered-messages"></a>Mensagens com letras mortas
Para espreitar as mensagens **escritas por Dead** de uma fila ou subscrição, a operação de espreitar deve ser executada na fila da letra morta associada à fila ou subscrição. Para mais informações, consulte [o acesso às filas de cartas mortas.](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)

## <a name="expired-messages"></a>Mensagens caducadas
As mensagens expiradas podem ser incluídas nos resultados devolvidos da operação Peek. As mensagens consumidas e expiradas são limpas por uma "recolha de lixo" assíncronea. Este passo pode não ocorrer necessariamente imediatamente após a expiração das mensagens. É por isso que uma operação de espreitar pode devolver mensagens que já expiraram. Estas mensagens serão removidas ou enviadas com letras mortas quando uma operação de receção for invocada na fila ou subscrição da próxima vez. Tenha este comportamento em mente ao tentar recuperar mensagens diferidas da fila. 

Uma mensagem expirada já não é elegível para recuperação regular por qualquer outro meio, mesmo quando é devolvida por Peek. A devolução destas mensagens é por design, uma vez que Peek é uma ferramenta de diagnóstico que reflete o estado atual do registo.

## <a name="locked-messages"></a>Mensagens bloqueadas
Peek também devolve mensagens que estavam **bloqueadas** e estão atualmente a ser processadas por outros recetores. No entanto, como Peek devolve uma imagem desligada, o estado de bloqueio de uma mensagem não pode ser observado em mensagens espreitadas.

## <a name="peek-apis"></a>Espreite as APIs
O Peek trabalha em filas, assinaturas e nas suas filas de cartas mortas. 

Quando chamado repetidamente, a operação de espreitar enumera todas as mensagens na fila ou subscrição, por ordem, do número de sequência mais baixo disponível para o mais alto. É a ordem em que as mensagens foram encosadas, não a ordem pela qual as mensagens podem eventualmente ser recuperadas.

Também pode passar um SequenceNumber para uma operação de espreitar. Será usado para determinar de onde começar a espreitar. Pode estotar chamadas subsequentes para a operação de espreitar sem especificar o parâmetro para enumerar ainda mais.

## <a name="next-steps"></a>Passos seguintes
Experimente as amostras no idioma à sua escolha para explorar a funcionalidade de navegação por mensagem ou espreitar:

- [Amostras da biblioteca de clientes do Azure Service Bus para Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Espreite uma** amostra de mensagem
- [Amostras da biblioteca do cliente do Azure Service Bus para Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek.py** amostra
- [Amostras de biblioteca de clientes do Azure Service Bus para JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.js** amostra
- [Amostras de biblioteca de clientes do Azure Service Bus para TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **amostra de navegaçãoMessages.ts**
- [Amostras Azure.Messaging.ServiceBus para .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - Consulte os métodos de esprevação das classes recetoras na [documentação de referência](/dotnet/api/azure.messaging.servicebus).

Encontre amostras para as bibliotecas clientes mais antigas .NET e Java abaixo:
- [Amostras microsoft.Azure.ServiceBus para .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  **Amostra de navegação de mensagens (Peek)** 
- [amostras de azure-servicebus para Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **Mensagem Navegue** na amostra. 
