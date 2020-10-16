---
title: Azure Event Hubs - Exceções do Gestor de Recursos Microsoft Docs
description: A lista de exceções do Azure Event Hubs surgiu pelo Azure Resource Manager e sugeriu ações.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334213"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs - Exceções do Gestor de Recursos
Este artigo lista exceções geradas ao interagir com os Azure Event Hubs usando o Azure Resource Manager - através de modelos ou chamadas diretas.

> [!IMPORTANT]
> Este documento é frequentemente atualizado. Por favor, verifique se há novidades.

As seguintes secções fornecem várias exceções/erros que são surgidos através do Azure Resource Manager.

## <a name="error-code-conflict"></a>Código de erro: Conflito

| Código de erro | Subcodificador de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflito | 40300 | O número máximo de recursos do tipo EventHub foi atingido ou ultrapassado. Atual: #, Max permitido: # | O espaço de nome atingiu a sua [quota](event-hubs-quotas.md) para o número de Centros de Eventos que pode conter. | Elimine quaisquer centros de eventos não reutilizados ou estranhos do espaço de nomes ou considere o upgrade para um [cluster dedicado](event-hubs-dedicated-overview.md). |
| Conflito | nenhuma | A recuperação de desastres (DR) config não pode ser eliminada porque a replicação está em andamento. Falhe ou quebre o emparelhamento antes de tentar eliminar o DR Config. | [A replicação do GeoDR](event-hubs-geo-dr.md) está em andamento, por isso o config não pode ser eliminado neste momento. | Para desbloquear a eliminação do config, espere até que a replicação esteja concluída, desencadeie uma falha ou quebre o emparelhamento geoDR. |
| Conflito | nenhuma | A atualização do Namespace falhou com o conflito no backend. | Outra operação está atualmente a ser feita neste espaço de nome. | Aguarde até que a operação atual termine e, em seguida, redaça. |

## <a name="error-code-429"></a>Código de erro: 429

| Código de erro | Subcodificador de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | nenhuma | Provisão de espaço de nome em transição | Outra operação está atualmente a ser feita neste espaço de nome. | Aguarde até que a operação atual termine e, em seguida, redaça. |
| 429 | nenhuma | Operação de recuperação de desastres em curso. | Uma operação [GeoDR](event-hubs-geo-dr.md) está atualmente a ser feita neste espaço de nome ou emparelhamento. | Aguarde até que a operação geoDR atual termine e, em seguida, redaça. |

## <a name="error-code-badrequest"></a>Código de erro: BadRequest

| Código de erro | Subcodificador de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | O PartitionCount não pode ser mudado para um centro de eventos. | O nível básico ou padrão dos Azure Event Hubs não suporta a mudança de divisórias. | Crie um novo centro de eventos com o número de divisórias procurado no seu espaço de nome básico ou padrão. A escala de partição é suportada para [clusters dedicados.](event-hubs-dedicated-overview.md) |
| BadRequest | 40000 | O valor '#' para MessageRetentionInDays não é válido para o nível Básico. o valor não pode exceder o dia de 1. | Os espaços de nome de eventos de nível básico suportam apenas a retenção de mensagens até 1 dia. | Se for desejada mais de um dia de retenção de mensagens, [crie um espaço de nome padrão para os Centros de Eventos](event-hubs-create.md). | 
| BadRequest | nenhuma | O nome especificado não está disponível. | Os nomes do espaço de nome devem ser únicos e o nome especificado já está tomado. | Se for o proprietário do espaço de nome existente com o nome especificado, pode eliminá-lo, o que causará a perda de dados. Então, tente de novo com o mesmo nome. Se o espaço de nome não for seguro para eliminar (ou não é o proprietário), escolha outro nome de espaço de nome. |
| BadRequest | nenhuma | A subscrição especificada atingiu a sua quota de espaços de nome. | A sua subscrição atingiu a [quota](event-hubs-quotas.md) para o número de espaços de nome que pode conter. | Considere eliminar espaços de nome não reutilizados nesta subscrição, criar outra subscrição ou atualizar para um [cluster dedicado.](event-hubs-dedicated-overview.md) |
| BadRequest | nenhuma | Não é possível atualizar um espaço de nome que seja secundário | O espaço de nome não pode ser atualizado porque é o espaço de nome secundário num [emparelhamento GeoDR.](event-hubs-geo-dr.md) | Se apropriado, faça a alteração para o espaço de nome primário neste emparelhamento. Caso contrário, quebre o emparelhamento geoDR para fazer a alteração. |
| BadRequest | nenhuma | Não pode definir Auto-Insuflado em SKU básico | O Auto-Inflate não pode ser ativado em espaços de nomes de centros de eventos de nível básico. | Para [ativar o Auto Inflate](event-hubs-auto-inflate.md) num espaço de nome, certifique-se de que é de nível padrão. |
| BadRequest | nenhuma | Não há capacidade suficiente para criar o espaço de nomes. Contacte o administrador do Event Hubs. | A região selecionada está na capacidade e mais espaços de nome não podem ser criados. | Selecione outra região para alojar o seu espaço de nome. |
| BadRequest | nenhuma | The operation can't be done on entity type 'ConsumerGroup' because the namespace 'namespace name' is using 'Basic' tier.  | Os espaços de nomes de eventos de nível básico têm uma quota (event-hubs-quotas.md) de um grupo de consumidores (o padrão). Criar mais grupos de consumidores não é apoiado. | Continue a utilizar o grupo de consumidores predefinido ($Default), ou se forem necessários mais, considere a utilização de um espaço de nome de padrão tier Event Hubs. | 
| BadRequest | nenhuma | O nome do "nome do espaço de nome" não existe. | O espaço de nome fornecido não foi encontrado. | Verifique duas vezes se o nome do espaço de nome está correto e pode ser encontrado na sua subscrição. Se não for, [crie um espaço de nomes de Event Hubs](event-hubs-create.md). | 
| BadRequest | nenhuma | A propriedade de localização do recurso não corresponde ao seu espaço de nome. | A criação de um centro de eventos numa região específica falhou porque não correspondia à região do espaço de nomes. | Tente criar o centro de eventos na mesma região que o espaço de nomes. | 

## <a name="error-code-internal-server-error"></a>Código de erro: Erro interno do servidor

| Código de erro | Subcodificador de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | nenhuma | Erro interno do servidor. | O serviço Event Hubs teve um erro interno. | Recandidutar a operação falhada. Se a operação continuar a falhar, contacte o suporte. |