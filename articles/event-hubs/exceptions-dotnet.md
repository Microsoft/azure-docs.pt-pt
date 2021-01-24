---
title: Azure Event Hubs - .NET exceções
description: Este artigo fornece uma lista de exceções de mensagens Azure Event Hubs .NET e ações sugeridas.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745907"
---
# <a name="eventhubsexception---net"></a>EventHubsException - .NET
Um **EventHubsException** é desencadeado quando uma operação específica do Event Hubs causou um problema, incluindo ambos os erros dentro do serviço e específicos para o cliente. 

## <a name="exception-information"></a>Informações de exceção
A exceção inclui as seguintes informações contextuais para ajudar a compreender o contexto do erro e a sua gravidade relativa. 

- **IsTransient**: Identifica se a exceção é considerada recuperável ou não. No caso em que foi considerada transitória, a política de recandidologia adequada já foi aplicada e as represálias não foram bem sucedidas.
- **Razão**: Fornece um conjunto de razões bem conhecidas para a falha que ajuda a categorizar e clarificar a causa principal. Estas razões destinam-se a permitir a aplicação de filtragem de exceções e outra lógica ao inspecionar o texto de uma mensagem de exceção não seria o ideal. Algumas razões fundamentais para falhar são:
    - **Cliente Fechado**: Ocorre quando um cliente do Event Hub que já foi fechado ou eliminado. Recomendamos que verifique o código de aplicação para garantir que os objetos da biblioteca do cliente do Event Hubs sejam criados e fechados no âmbito pretendido.
    - **Tempo de serviço**: Indica que o serviço Desematar Hubs não respondeu a uma operação dentro do tempo esperado. Este problema pode ter sido causado por um problema transitório de rede ou problema de serviço. O serviço Event Hubs pode ou não ter concluído com sucesso o pedido; o estado não é conhecido. Recomenda-se tentar verificar o estado atual e tentar novamente, se necessário.
    - **Quota Excedida**: Indica que existem demasiadas operações de leitura ativas para um único grupo de consumidores. Este limite depende do nível do espaço de nomes do Event Hubs, e pode ser necessário mudar-se para um nível mais alto. Uma alternativa seria criar grupos de consumidores adicionais e garantir que o número de clientes de consumo que lêem para qualquer grupo está dentro do limite. Para mais informações, consulte [as quotas e limites do Azure Event Hubs.](event-hubs-quotas.md)
    - **Tamanho da mensagem Excedido**: Dados do evento como um tamanho máximo permitido tanto para um evento individual como um lote de eventos. Inclui os dados do evento, e quaisquer metadados associados e sobrecarga do sistema. Para resolver este erro, reduza o número de eventos enviados num lote ou reduza o tamanho dos dados incluídos na mensagem. Como os limites de tamanho estão sujeitos a alterações, consulte as quotas e limites para [especificidades do Azure Event Hubs.](event-hubs-quotas.md)
    - **Desligado do Consumidor**: Um cliente de consumo foi desligado pelo serviço Event Hub a partir da instância Event Hub. Ocorre tipicamente quando um consumidor com um nível de proprietário mais elevado afirma a propriedade sobre um emparelhamento de divisórias e grupo de consumidores.
    - **Recurso Não Encontrado**: O serviço Desemaparsos não conseguiu encontrar um recurso como um centro de eventos, um grupo de consumidores ou uma divisória. Pode ter sido apagado ou que há um problema com o próprio serviço de Centros de Eventos.

## <a name="handling-exceptions"></a>Processamento de exceções
Pode reagir a uma razão específica de falha para o **EventHubException**  de várias maneiras. Uma das formas é aplicar uma cláusula de filtro de exceção como parte do bloco de capturas.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Próximos passos
Existem outras exceções que estão documentadas no [artigo do legado.](event-hubs-messaging-exceptions.md) Alguns deles aplicam-se apenas à biblioteca de clientes legacy Event Hubs .NET.