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
ms.openlocfilehash: 9a2d25aba03156d6d14fe5ef9aa58b3748033b85
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296383"
---
# <a name="service-bus-resource-manager-exceptions"></a>Exceções do Gerenciador de recursos do barramento de serviço

Este artigo lista exceções geradas ao interagir com o barramento de serviço do Azure usando modelos Azure Resource Manager ou chamadas diretas.

> [!IMPORTANT]
> Este documento é atualizado com frequência. Verifique novamente se há atualizações.

Abaixo estão as várias exceções/erros que são exibidos por meio do Azure Resource Manager.

## <a name="error-bad-request"></a>Erro: solicitação inadequada

"Solicitação inadequada" implica que a solicitação recebida pelo Gerenciador de recursos falhou na validação.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Pedido Incorreto | 40000 | Subcódigo = 40000. A propriedade *' nome da propriedade '* não pode ser definida ao criar uma fila porque o namespace *' nome do namespace '* está usando a camada ' básica '. Esta operação só tem suporte na camada ' Standard ' ou ' Premium '. | Na camada básica do barramento de serviço do Azure, as propriedades abaixo não podem ser definidas ou atualizadas- <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Tópicos </li> </ul> | Considere atualizar da camada básica para Standard ou Premium para usar essa funcionalidade. |
| Pedido Incorreto | 40000 | Subcódigo = 40000. O valor da propriedade ' requiresDuplicateDetection ' de uma fila (ou tópico) existente não pode ser alterado. | A detecção de duplicidades deve ser habilitada/desabilitada no momento da criação da entidade. O parâmetro de configuração de detecção de duplicidades não pode ser alterado após a criação. | Para habilitar a detecção de duplicidades em uma fila/tópico criado anteriormente, você pode criar uma nova fila/tópico com detecção de duplicidades e, em seguida, encaminhar da fila original para a nova fila/tópico. |
| Pedido Incorreto | 40000 | Subcódigo = 40000. O valor especificado 16384 é inválido. A propriedade ' MaxSizeInMegabytes ' deve ser um dos seguintes valores: 1024; 2048; 3072; 4096; 5120. | O valor de MaxSizeInMegabytes é inválido. | Verifique se o MaxSizeInMegabytes é um dos seguintes-1024, 2048, 3072, 4096, 5120. |
| Pedido Incorreto | 40000 | Subcódigo = 40000. O particionamento não pode ser alterado para a fila/tópico. | O particionamento não pode ser alterado para a entidade. | Crie uma nova entidade (fila ou tópico) e habilite partições. | 
| Pedido Incorreto | nenhuma | O namespace *' nome do namespace '* não existe. | O namespace não existe em sua assinatura do Azure. | Para resolver esse erro, tente o seguinte <ul> <li> Verifique se a assinatura do Azure está correta. </li> <li> Verifique se o namespace existe. </li> <li> Verifique se o nome do namespace está correto (sem erros de ortografia ou cadeias de caracteres nulas). </li> </ul> | 
| Pedido Incorreto | 40400 | Subcódigo = 40400. A entidade de destino de encaminhamento automático não existe. | O destino para a entidade de destino de encaminhamento automático não existe. | A entidade de destino (fila ou tópico) deve existir antes que a origem seja criada. Tente novamente depois de criar a entidade de destino. |
| Pedido Incorreto | 40000 | Subcódigo = 40000. O tempo de bloqueio fornecido excede o máximo permitido de ' 5 ' minutos. | A hora para a qual uma mensagem pode ser bloqueada deve estar entre 1 minuto (mínimo) e 5 minutos (máximo). | Verifique se o tempo de bloqueio fornecido está entre 1 minuto e 5 minutos. |
| Pedido Incorreto | 40000 | Subcódigo = 40000. A propriedade DelayedPersistence e RequiresDuplicateDetection não podem ser habilitadas juntas. | As entidades com detecção de duplicidades habilitadas nelas devem ser persistentes, portanto, a persistência não pode ser atrasada. | Saiba mais sobre [detecção de duplicidades](duplicate-detection.md) |
| Pedido Incorreto | 40000 | Subcódigo = 40000. O valor da propriedade RequiresSession de uma fila existente não pode ser alterado. | O suporte para sessões deve ser habilitado no momento da criação da entidade. Depois de criado, você não pode habilitar/desabilitar sessões em uma entidade existente (fila ou assinatura) | Exclua e recrie uma nova fila (ou assinatura) com a propriedade "RequiresSession" habilitada. |
| Pedido Incorreto | 40000 | Subcódigo = 40000. ' URI_PATH ' contém caractere (s) que não é permitido pelo barramento de serviço. Os segmentos de entidade podem conter apenas letras, números, pontos (.), hifens (-) e sublinhados (_). | Os segmentos de entidade podem conter apenas letras, números, pontos (.), hifens (-) e sublinhados (_). Quaisquer outros caracteres causam a falha da solicitação. | Verifique se não há caracteres inválidos no caminho do URI. |


## <a name="error-code-429"></a>Código de erro: 429

Assim como no HTTP, "código de erro 429" indica "muitas solicitações". Isso implica que o recurso específico (namespace) está sendo limitado devido a muitas solicitações (ou devido a operações conflitantes) nesse recurso.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcódigo = 50004. A solicitação foi encerrada porque o namespace *do namespace* está sendo limitado. | Essa condição de erro é pressionada quando o número de solicitações de entrada excede a limitação do recurso. | Aguarde alguns segundos e tente novamente. <br/> <br/> Saiba mais sobre as [cotas](service-bus-quotas.md) e os [limites de solicitação de Azure Resource Manager](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Subcódigo = 40901. Outra operação conflitante está em andamento. | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação em andamento atual antes de tentar novamente. |
| 429 | 40900 | Subcódigo = 40900. Houver. Você está solicitando uma operação que não é permitida no estado atual do recurso. | Essa condição pode ser atingida quando várias solicitações são feitas para executar as operações na mesma entidade (fila, tópico, assinatura ou regra) ao mesmo tempo. | Aguarde alguns segundos e tente novamente |
| 429 | 40901 | A solicitação na entidade *' nome da entidade '* entrou em conflito com outra solicitação | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação anterior antes de tentar novamente |
| 429 | 40901 | Outra solicitação de atualização está em andamento para a entidade *' nome da entidade '* . | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação anterior antes de tentar novamente |
| 429 | nenhuma | Conflito de recursos ocorrido. Outra operação conflitante pode estar em andamento. Se esta for uma nova tentativa para uma operação com falha, a limpeza em segundo plano ainda estará pendente. Tente novamente mais tarde. | Essa condição pode ser atingida quando há uma operação pendente na mesma entidade. | Aguarde a conclusão da operação anterior antes de tentar novamente. |


## <a name="error-code-not-found"></a>Código de erro: não encontrado

Essa classe de erros indica que o recurso não foi encontrado.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não encontrado | nenhuma | A entidade *' nome da entidade '* não foi encontrada. | A entidade na qual a operação não foi encontrada. | Verifique se a entidade existe e tente a operação novamente. |
| Não encontrado | nenhuma | Não encontrado. A operação não existe. | A operação que você está tentando executar não existe. | Verifique a operação e tente novamente. |
| Não encontrado | nenhuma | A solicitação de entrada não é reconhecida como uma solicitação de Put de política de namespace. | O corpo da solicitação de entrada é nulo e, portanto, não pode ser executado como uma solicitação Put. | Verifique o corpo da solicitação para garantir que ele não seja nulo. | 
| Não encontrado | nenhuma | Não foi possível encontrar a entidade de mensagens *' nome da entidade '* . | A entidade na qual você está tentando executar a operação não foi encontrada. | Verifique se a entidade existe e tente a operação novamente. |
