---
title: Azure Event Hubs - Fontes de Gestor de Recursos Microsoft Docs
description: Lista de Hubs de Eventos Azure exceções surgiram pelo Gestor de Recursos Azure e sugeriu ações.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936090"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs - Exceções ao Gestor de Recursos
Este artigo lista exceções geradas ao interagir com os Hubs de Eventos Azure usando o Gestor de Recursos Azure - através de modelos ou chamadas diretas.

> [!IMPORTANT]
> Este documento é frequentemente atualizado. Por favor, verifique se há novidades.

As seguintes secções fornecem várias exceções/erros que são surgidas através do Gestor de Recursos Azure.

## <a name="error-code-conflict"></a>Código de erro: Conflito

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflito | 40300 | O número máximo de recursos do tipo EventHub foi atingido ou ultrapassado. Real: #, Max permitido: # | O espaço de nome atingiu a sua [quota](event-hubs-quotas.md) para o número de Centros de Eventos que pode conter. | Elimine quaisquer centros de eventos não utilizados ou estranhos do espaço de nome ou considere a atualização para um [cluster dedicado](event-hubs-dedicated-overview.md). |
| Conflito | nenhuma | A recuperação de desastres (DR) config não pode ser eliminada porque a replicação está em andamento. Fail over ou break pairing antes de tentar apagar o DR Config. | [A replicação do GeoDR](event-hubs-geo-dr.md) está em andamento, por isso a config não pode ser eliminada neste momento. | Para desbloquear a eliminação do config, ou aguarde até que a replicação esteja concluída, desencadeie uma falha ou quebre o emparelhamento GeoDR. |
| Conflito | nenhuma | A atualização do espaço de nome falhou com o conflito no backend. | Outra operação está a ser feita neste espaço de nome. | Aguarde até que a operação atual termine e, em seguida, tente novamente. |

## <a name="error-code-429"></a>Código de erro: 429

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | nenhuma | Fornecimento de espaço de nome em transição | Outra operação está a ser feita neste espaço de nome. | Aguarde até que a operação atual termine e, em seguida, tente novamente. |
| 429 | nenhuma | Operação de recuperação de desastres em curso. | Atualmente está a ser feita uma operação [GeoDR](event-hubs-geo-dr.md) neste espaço de nome ou emparelhamento. | Aguarde até que a operação GeoDR atual termine e, em seguida, tente novamente. |

## <a name="error-code-badrequest"></a>Código de erro: BadRequest

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount não pode ser mudado para um centro de eventos. | O nível básico ou padrão dos Hubs de Eventos Azure não suporta a mudança de divisórias. | Crie um novo centro de eventos com o número de divisórias procurados no seu espaço de nome de nível básico ou padrão. A escala de partição é suportada para [clusters dedicados](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | O valor '#' para MessageRetentionInDays não é válido para o nível Básico. o valor não pode exceder '1' dia(s). | Os espaços de nome de nível básico hubs apenas suportam a retenção de mensagens até 1 dia. | Se for desejada mais de um dia de retenção de mensagens, crie um espaço de nome padrão [do Event Hubs](event-hubs-create.md). | 
| BadRequest | nenhuma | O nome especificado não está disponível. | Os nomes do espaço de nome devem ser únicos e o nome especificado já está tomado. | Se for o proprietário do espaço de nome existente com o nome especificado, pode eliminá-lo, o que irá causar perda de dados. Então, tente de novo com o mesmo nome. Se o espaço de nome não for seguro para apagar (ou não é o proprietário), escolha outro nome de espaço de nome. |
| BadRequest | nenhuma | A subscrição especificada atingiu a sua quota de espaços de nome. | A sua subscrição atingiu a [quota](event-hubs-quotas.md) para o número de espaços de nome que pode deter. | Considere eliminar espaços de nomenão utilizados nesta subscrição, criar outra subscrição ou melhorar para um [cluster dedicado.](event-hubs-dedicated-overview.md) |
| BadRequest | nenhuma | Não pode atualizar um espaço de nome que é secundário | O espaço de nome não pode ser atualizado porque é o espaço de nome secundário num [emparelhamento GeoDR.](event-hubs-geo-dr.md) | Se for caso disso, faça a alteração para o espaço de nome principal neste emparelhamento. Caso contrário, quebre o emparelhamento GeoDR para fazer a mudança. |
| BadRequest | nenhuma | Não pode configurar auto-inflação em SKU básico | O Auto-Inflate não pode ser ativado nos espaços de nome de nível básico do Event Hubs. | Para [ativar o Auto Inflate](event-hubs-auto-inflate.md) num espaço de nome, certifique-se de que é de nível padrão. |
| BadRequest | nenhuma | Não há capacidade suficiente para criar o espaço de nome. Contacte o administrador do Event Hubs. | A região selecionada está em capacidade e não é possível criar mais espaços de nome. | Selecione outra região para alojar o seu espaço de nome. |
| BadRequest | nenhuma | A operação não pode ser feita no tipo de entidade 'ConsumerGroup' porque o nome do espaço de nome 'namespace' está a utilizar o nível 'Basic'.  | Os espaços de nome de nível básico dos Centros de Eventos têm uma [quota]((event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits) de um grupo de consumidores (o padrão). A criação de mais grupos de consumidores não é apoiada. | Continue a utilizar o grupo de consumidores padrão ($Default), ou se for necessário mais, considere utilizar um espaço de nome de nível padrão do Event Hubs. | 
| BadRequest | nenhuma | O espaço de nome "nome do espaço de nome" não existe. | O espaço de nome fornecido não podia ser encontrado. | Verifique duas vezes se o nome do espaço de nome está correto e pode ser encontrado na sua subscrição. Se não for, crie um espaço de [nome de Event Hubs.](event-hubs-create.md) | 
| BadRequest | nenhuma | A propriedade de localização do recurso não corresponde ao seu espaço de nome contendo. | A criação de um centro de eventos numa região específica falhou porque não correspondia à região do espaço de nome. | Tente criar o centro de eventos na mesma região que o espaço de nome. | 

## <a name="error-code-internal-server-error"></a>Código de erro: Erro interno do servidor

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | nenhuma | Erro interno do servidor. | O serviço De Eventos Hubs teve um erro interno. | Tente novamente a operação falhada. Se a operação continuar a falhar, contacte o suporte. |