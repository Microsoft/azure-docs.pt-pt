---
title: Exceções do Gerenciador de recursos do barramento de serviço do Azure | Microsoft Docs
description: Lista de exceções do barramento de serviço na superfície por Azure Resource Manager e ações sugeridas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329308"
---
# <a name="service-bus-resource-manager-exceptions"></a>Exceções do Gerenciador de recursos do barramento de serviço

Este artigo lista exceções geradas ao interagir com o barramento de serviço do Azure usando modelos Azure Resource Manager ou chamadas diretas.

> [!IMPORTANT]
> Este documento é atualizado com frequência. Verifique novamente se há atualizações.

Abaixo estão as várias exceções/erros que são exibidos por meio do Azure Resource Manager.

## <a name="error-bad-request"></a>Erro: Pedido Inválido

"Solicitação inadequada" implica que a solicitação recebida pelo Gerenciador de recursos falhou na validação.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Pedido Inválido | 40000 | Subcódigo = 40000. A propriedade *' nome da propriedade '* não pode ser definida ao criar uma fila porque o namespace *' nome do namespace '* está usando a camada ' básica '. Esta operação só tem suporte na camada ' Standard ' ou ' Premium '. | Na camada básica do barramento de serviço do Azure, as propriedades abaixo não podem ser definidas ou atualizadas- <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Tópicos </li> </ul> | Considere a atualização da camada básica para Standard ou Premium para aproveitar essa funcionalidade. |
| Pedido Inválido | 40000 | Subcódigo = 40000. O valor da propriedade ' requiresDuplicateDetection ' de uma fila existente não pode ser alterado. | A detecção de duplicidades deve ser habilitada/desabilitada no momento da criação da entidade. Depois de criado, o parâmetro de configuração de detecção de duplicidades não pode ser alterado. | Para habilitar a detecção de duplicidades em uma fila/assinatura criada anteriormente, você pode criar uma nova fila com detecção de duplicidades e, em seguida, encaminhar da fila original para a nova fila. |
| Pedido Inválido | 40000 | Subcódigo = 40000. O valor especificado 16384 é inválido. A propriedade ' MaxSizeInMegabytes ' deve ser um dos seguintes valores: 1024; 2048; 3072; 4096; 5120. | O valor de MaxSizeInMegabytes é inválido. | Verifique se o MaxSizeInMegabytes é um dos seguintes-1024, 2048, 3072, 4096, 5120. |
| Pedido Inválido | 40000 | Subcódigo = 40000. O particionamento não pode ser alterado para a fila. | O particionamento não pode ser alterado para a entidade. | Crie uma nova entidade e habilite partições. | 
| Pedido Inválido | nenhum | O namespace *' nome do namespace '* não existe. | O namespace não existe em sua assinatura do Azure. | Para resolver esse erro, tente o seguinte <ul> <li> Verifique se a assinatura do Azure está correta. </li> <li> Verifique se o namespace existe. </li> <li> Verifique se o nome do namespace está correto (sem erros de ortografia ou cadeias de caracteres nulas). </li> </ul> | 
| Pedido Inválido | 40400 | Subcódigo = 40400. A entidade de destino de encaminhamento automático não existe. | O destino para a entidade de destino de encaminhamento automático não existe. | A entidade de destino (fila ou tópico) deve existir antes que a origem seja criada. Tente novamente depois de criar a entidade de destino. |


## <a name="error-code-429"></a>Código de erro: 429

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcódigo = 50004. A solicitação foi encerrada porque o namespace *do namespace* está sendo limitado. | Essa condição de erro é pressionada quando o número de solicitações de entrada excede a limitação do recurso. | Aguarde alguns segundos e tente novamente. <br/> <br/> Saiba mais sobre as [cotas](service-bus-quotas.md) e os [limites de solicitação de Azure Resource Manager](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Subcódigo = 40901. Outra operação conflitante está em andamento. | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação em andamento atual antes de tentar novamente. |
| 429 | 40900 | Subcódigo = 40900. Houver. Você está solicitando uma operação que não é permitida no estado atual do recurso. | Essa condição pode ser atingida quando várias solicitações são feitas para executar as operações na mesma entidade (fila, tópico, assinatura ou regra) ao mesmo tempo | Aguarde alguns segundos e tente novamente |
| 429 | nenhum | Conflito de recursos ocorrido. Outra operação conflitante pode estar em andamento. Se esta for uma nova tentativa para uma operação com falha, a limpeza em segundo plano ainda estará pendente. Tente novamente mais tarde. | Essa condição pode ser atingida quando há uma operação pendente na mesma entidade. | Aguarde a conclusão da operação anterior antes de tentar novamente. |

