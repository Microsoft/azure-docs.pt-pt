---
title: Exceções ao Gestor de Recursos de Autocarros de Serviço sintetizar a Azure Service Bus Resource Manager [ Microsoft Docs
description: Lista de exceções de ônibus de serviço surgiu por Azure Resource Manager e sugeriu ações.
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
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978261"
---
# <a name="service-bus-resource-manager-exceptions"></a>Exceções ao Gestor de Recursos de Autocarros de Serviços

Este artigo lista exceções geradas ao interagir com o Azure Service Bus utilizando o Gestor de Recursos Azure - através de modelos ou chamadas diretas.

> [!IMPORTANT]
> Este documento é frequentemente atualizado. Por favor, verifique se há novidades.

Abaixo estão as várias exceções/erros que são surgidas através do Gestor de Recursos Azure.

## <a name="error-bad-request"></a>Erro: Pedido mau

"Pedido Mau" implica que o pedido recebido pelo Gestor de Recursos não foi validado.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Pedido Incorreto | 40000 | Subcode=40000. O *'nome de propriedade'* da propriedade não pode ser definido na criação de uma fila porque o nome do espaço de nome *'namespace'* está a usar o 'Basic' Tier. Esta operação só é suportada em nível 'Standard' ou 'Premium'. | No Azure Service Bus Basic Tier, as propriedades abaixo não podem ser definidas ou atualizadas - <ul> <li> RequerDetecção duplicada </li> <li> AutodeleteOnidle </li> <li>RequerSSession</li> <li>Tempo de mensagem padrãopara viver </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> Para a frente </li> <li> Tópicos </li> </ul> | Considere atualizar de nível Basic para Standard ou Premium para utilizar esta funcionalidade. |
| Pedido Incorreto | 40000 | Subcode=40000. O valor para a propriedade 'requerDuplicateDetection' de uma fila ou tópico existente não pode ser alterado. | A deteção duplicada deve ser ativada/desativada no momento da criação da entidade. O parâmetro de configuração de deteção duplicado não pode ser alterado após a criação. | Para permitir a deteção de duplicados numa fila/tópico previamente criado, pode criar uma nova fila/tópico com deteção duplicada e, em seguida, avançar da fila original para a nova fila/tópico. |
| Pedido Incorreto | 40000 | Subcode=40000. O valor especificado 16384 é inválido. A propriedade 'MaxSizeInMegabytes', deve ser um dos seguintes valores: 1024;2048;3072;4096;5120. | O valor MaxSizeInMegabytes é inválido. | Certifique-se de que o MaxSizeInMegabytes é um dos seguintes - 1024, 2048, 3072, 4096, 5120. |
| Pedido Incorreto | 40000 | Subcode=40000. A partilha não pode ser alterada para fila/tópico. | A partilha não pode ser alterada para entidade. | Criar uma nova entidade (fila ou tópico) e ativar divisórias. | 
| Pedido Incorreto | nenhuma | O espaço de nome *'namespace'* não existe. | O espaço de nome não existe dentro da sua subscrição Azure. | Para resolver este erro, por favor tente o abaixo <ul> <li> Certifique-se de que a Subscrição Azure está correta. </li> <li> Certifique-se de que o espaço de nomeexiste. </li> <li> Verifique se o nome do espaço de nome está correto (sem erros ortográficos ou cordas nulas). </li> </ul> | 
| Pedido Incorreto | 40400 | Subcódigo=40400. A entidade de destino de reencaminhamento automático não existe. | O destino para a entidade de destino de reencaminhamento automático não existe. | A entidade de destino (fila ou tópico), deve existir antes da criação da fonte. Voltar a tentar depois de criar a entidade de destino. |
| Pedido Incorreto | 40000 | Subcode=40000. O tempo de bloqueio fornecido excede o máximo permitido de minutos de '5'. | O tempo para o qual uma mensagem pode ser bloqueada deve ser entre 1 minuto (mínimo) e 5 minutos (máximo). | Certifique-se de que o tempo de bloqueio fornecido é entre 1 min e 5 minutos. |
| Pedido Incorreto | 40000 | Subcode=40000. Tanto a propriedade Retardada Persistência como requer a propriedade DuplicateDetection não podem ser ativadas em conjunto. | As entidades com deteção duplicada ativadas devem ser persistentes, pelo que a persistência não pode ser adiada. | Saiba mais sobre [a Deteção de Duplicados](duplicate-detection.md) |
| Pedido Incorreto | 40000 | Subcode=40000. O valor para a propriedade RequiresSession de uma fila existente não pode ser alterado. | O apoio às sessões deve ser ativado no momento da criação de entidades. Uma vez criado, não é possível ativar/desativar sessões numa entidade existente (fila ou subscrição) | Eliminar e recriar uma nova fila (ou subscrição) com a propriedade "RequiresSession" ativada. |
| Pedido Incorreto | 40000 | Subcode=40000. 'URI_PATH' contém caracteres que não são permitidos pela Service Bus. Os segmentos de entidades só podem conter letras, números, períodos(.), hífenes (-) e sublinhados (_). | Os segmentos de entidades só podem conter letras, números, períodos(.), hífenes (-) e sublinhados (_). Qualquer outro caracteres faz com que o pedido falhe. | Certifique-se de que não existem caracteres inválidos no Caminho URI. |


## <a name="error-code-429"></a>Código de erro: 429

Tal como em HTTP, o "Error code 429" indica "pedidos a mais". Implica que o recurso específico (espaço de nome) está a ser estrangulado devido a demasiados pedidos (ou devido a operações contraditórias) sobre esse recurso.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcódigo=50004. O pedido foi cancelado porque o espaço de nome *do seu espaço* de nome está a ser estrangulado. | Esta condição de erro é atingida quando o número de pedidos de entrada excede a limitação do recurso. | Espere alguns segundos e tente de novo. <br/> <br/> Saiba mais sobre as [quotas](service-bus-quotas.md) e limites de pedidos do Gestor de [Recursos Azure](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Subcódigo=40901. Outra operação conflituosa está em curso. | Outra operação conflituosa está em curso sobre o mesmo recurso/entidade | Aguarde que a atual operação em curso esteja concluída antes de tentar novamente. |
| 429 | 40900 | Subcódigo=40900. Um conflito. Está a pedir uma operação que não seja permitida no estado atual do recurso. | Esta condição pode ser atingida quando são feitos vários pedidos para realizar as operações na mesma entidade (fila, tópico, subscrição ou regra) ao mesmo tempo. | Espere por alguns segundos e tente novamente |
| 429 | 40901 | Pedido sobre *'nome de entidade'* conflituoso com outro pedido | Outra operação conflituosa está em curso sobre o mesmo recurso/entidade | Aguarde que a operação anterior termine antes de tentar novamente |
| 429 | 40901 | Está em curso outro pedido de atualização para o *'nome da entidade'.* | Outra operação conflituosa está em curso sobre o mesmo recurso/entidade | Aguarde que a operação anterior termine antes de tentar novamente |
| 429 | nenhuma | Conflito de recursos ocorreu. Outra operação conflituosa pode estar em curso. Se isto for uma nova tentativa de operação falhada, a limpeza de antecedentes ainda está pendente. Tente novamente mais tarde. | Esta condição pode ser atingida quando houver uma operação pendente contra a mesma entidade. | Aguarde que a operação anterior esteja concluída antes de tentar novamente. |


## <a name="error-code-not-found"></a>Código de erro: Não Encontrado

Esta classe de erros indica que o recurso não foi encontrado.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não encontrado | nenhuma | Entidade *'nome de entidade'* não foi encontrada. | A entidade contra a qual a operação não foi encontrada. | Verifique se a entidade existe e tente novamente a operação. |
| Não encontrado | nenhuma | Não encontrado. A Operação não existe. | A operação que está a tentar realizar não existe. | Verifique a operação e tente de novo. |
| Não encontrado | nenhuma | O pedido de entrada não é reconhecido como um pedido de política de espaço de nome. | O órgão de pedido de entrada é nulo e, portanto, não pode ser executado como um pedido de colocação. | Verifique o organismo de pedido para se certificar de que não é nulo. | 
| Não encontrado | nenhuma | O *"nome da entidade de mensagens"* não foi encontrado. | A entidade contra a qual está a tentar executar a operação não foi encontrada. | Por favor, verifique se a entidade existe e tente a operação novamente. |

## <a name="error-code-internal-server-error"></a>Código de erro: Erro do servidor interno

Esta classe de erros indica que houve um erro interno do servidor

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | 50000 | Subcode=50000. Erro interno do servidor| Pode acontecer por várias razões. Alguns dos sintomas são... <ul> <li> O pedido/corpo do cliente é corrupto e leva a um erro. </li> <li> O pedido do cliente foi cronometrado devido a problemas de processamento no serviço. </li> </ul> | Para resolver isto <ul> <li> Certifique-se de que os parâmetros dos pedidos não são nulos ou mal informados. </li> <li> Tente o pedido. </li> </ul> |

## <a name="error-code-unauthorized"></a>Código de erro: Não autorizado

Esta classe de erros indica a ausência de autorização para dirigir o comando.

| Código de erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não autorizado | nenhuma | Operação inválida no espaço de nome secundário. O espaço de nome secundário é apenas para leitura. | A operação foi realizada contra o espaço de nome secundário, que é configurado como um espaço de nome sem nome. | Tente o comando contra o espaço de nome principal. Saiba mais sobre [o espaço de nome secundário](service-bus-geo-dr.md) |
| Não autorizado | nenhuma | DesaparecidoToken: O cabeçalho de autorização não foi encontrado. | Este erro ocorre quando a autorização tem valores nulos ou incorretos. | Certifique-se de que o valor simbólico mencionado no cabeçalho de autorização está correto e não nulo. |