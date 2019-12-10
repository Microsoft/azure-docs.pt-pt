---
title: Hubs de eventos do Azure-exceções do Gerenciador de recursos | Microsoft Docs
description: Lista de exceções de hubs de eventos do Azure na superfície por Azure Resource Manager e ações sugeridas.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 6e42ce0ca0a7ac572398e9d024fcf69906670d74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74936090"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Hubs de eventos do Azure-exceções do Gerenciador de recursos
Este artigo lista exceções geradas ao interagir com hubs de eventos do Azure usando modelos Azure Resource Manager ou chamadas diretas.

> [!IMPORTANT]
> Este documento é atualizado com frequência. Verifique novamente se há atualizações.

As seções a seguir fornecem várias exceções/erros que são exibidos por meio de Azure Resource Manager.

## <a name="error-code-conflict"></a>Código de erro: conflito

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflito | 40300 | O número máximo de recursos do tipo EventHub foi atingido ou foi excedido. Real: #, máximo permitido: # | O namespace atingiu sua [cota](event-hubs-quotas.md) para o número de hubs de eventos que ele pode conter. | Exclua os hubs de eventos não utilizados ou estranhos do namespace ou considere atualizar para um [cluster dedicado](event-hubs-dedicated-overview.md). |
| Conflito | nenhuma | A configuração de recuperação de desastre (DR) não pode ser excluída porque a replicação está em andamento. Faça failover ou interrompa o emparelhamento antes de tentar excluir a configuração de DR. | A [replicação do GeoDR](event-hubs-geo-dr.md) está em andamento, portanto, a configuração não pode ser excluída no momento. | Para desbloquear a exclusão da configuração, aguarde até que a replicação seja concluída, dispare um failover ou interrompa o emparelhamento GeoDR. |
| Conflito | nenhuma | Falha na atualização do namespace com conflito no back-end. | Outra operação está sendo feita atualmente neste namespace. | Aguarde até que a operação atual seja concluída e tente novamente. |

## <a name="error-code-429"></a>Código de erro: 429

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | nenhuma | Provisionamento de namespace em transição | Outra operação está sendo feita atualmente neste namespace. | Aguarde até que a operação atual seja concluída e tente novamente. |
| 429 | nenhuma | Operação de recuperação de desastre em andamento. | Uma operação [GeoDR](event-hubs-geo-dr.md) está sendo feita atualmente neste namespace ou emparelhamento. | Aguarde até que a operação GeoDR atual seja concluída e tente novamente. |

## <a name="error-code-badrequest"></a>Código de erro: BadRequest

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount não pode ser alterado para um hub de eventos. | A camada básica ou Standard dos hubs de eventos do Azure não dá suporte à alteração de partições. | Crie um novo hub de eventos com o número desejado de partições no namespace da camada básica ou Standard. A expansão da partição tem suporte para [clusters dedicados](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | O valor ' # ' para MessageRetentionInDays não é válido para a camada básica. o valor não pode exceder ' 1 ' dia (s). | Os namespaces de hubs de eventos de camada básica dão suporte apenas à retenção de mensagens de até 1 dia. | Se mais de um dia de retenção de mensagem for desejado, [crie um namespace de hubs de eventos padrão](event-hubs-create.md). | 
| BadRequest | nenhuma | O nome especificado não está disponível. | Os nomes de namespace devem ser exclusivos e o nome especificado já está em uso. | Se você for o proprietário do namespace existente com o nome especificado, poderá excluí-lo, o que causará perda de dados. Em seguida, tente novamente com o mesmo nome. Se o namespace não for seguro para excluir (ou se você não for o proprietário), escolha outro nome de namespace. |
| BadRequest | nenhuma | A assinatura especificada atingiu sua cota de namespaces. | Sua assinatura atingiu a [cota](event-hubs-quotas.md) para o número de namespaces que ele pode conter. | Considere a exclusão de namespaces não utilizados nesta assinatura, a criação de outra assinatura ou a atualização para um [cluster dedicado](event-hubs-dedicated-overview.md). |
| BadRequest | nenhuma | Não é possível atualizar um namespace que seja secundário | O namespace não pode ser atualizado porque é o namespace secundário em um [emparelhamento GeoDR](event-hubs-geo-dr.md). | Se apropriado, faça a alteração no namespace primário neste emparelhamento. Caso contrário, quebre o emparelhamento GeoDR para fazer a alteração. |
| BadRequest | nenhuma | Não é possível definir o inflar automaticamente no SKU básico | O desinflar automaticamente não pode ser habilitado em namespaces de hubs de eventos de camada básica. | Para [habilitar o inflar automaticamente](event-hubs-auto-inflate.md) em um namespace, verifique se ele é da camada Standard. |
| BadRequest | nenhuma | Não há capacidade suficiente para criar o namespace. Contate o administrador dos hubs de eventos. | A região selecionada está na capacidade e mais namespaces não podem ser criados. | Selecione outra região para alojar seu namespace. |
| BadRequest | nenhuma | A operação não pode ser feita no tipo de entidade ' Consumer ' porque o namespace ' nome do namespace ' está usando a camada ' Basic '.  | Os namespaces de hubs de eventos de camada básica têm uma [cota] ((eventos-hubs-cotas. MD # evento-hubs-Basic-and-Standard---cotas-and-Limits) de um grupo de consumidores (o padrão). Não há suporte para a criação de mais grupos de consumidores. | Continue usando o grupo de consumidores padrão ($Default) ou, se houver mais necessidade, considere usar um namespace da camada Standard de hubs de eventos em vez disso. | 
| BadRequest | nenhuma | O namespace ' nome do namespace ' não existe. | O namespace fornecido não pôde ser encontrado. | Verifique se o nome do namespace está correto e pode ser encontrado em sua assinatura. Se não estiver, [crie um namespace de hubs de eventos](event-hubs-create.md). | 
| BadRequest | nenhuma | A propriedade Location do recurso não corresponde ao seu namespace recipiente. | A criação de um hub de eventos em uma região específica falhou porque não correspondeu à região do namespace. | Tente criar o Hub de eventos na mesma região que o namespace. | 

## <a name="error-code-internal-server-error"></a>Código de erro: erro interno do servidor

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | nenhuma | Erro interno do servidor. | O serviço de hubs de eventos teve um erro interno. | Repita a operação com falha. Se a operação continuar falhando, contate o suporte. |