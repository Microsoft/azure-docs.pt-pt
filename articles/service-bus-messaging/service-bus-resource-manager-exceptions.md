---
title: Azure Service Bus Resource Manager exceções / Microsoft Docs
description: Lista de exceções de Service Bus surgiu pelo Azure Resource Manager e sugeriu ações.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a0b0338da0f002c7b667748ffd2bf5a40c91c580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85336965"
---
# <a name="service-bus-resource-manager-exceptions"></a>Exceções do Gestor de Recursos de Autocarros de Serviço

Este artigo lista as exceções geradas ao interagir com o Azure Service Bus utilizando o Azure Resource Manager - através de modelos ou chamadas diretas.

> [!IMPORTANT]
> Este documento é frequentemente atualizado. Por favor, verifique se há novidades.

Abaixo estão as várias exceções/erros que são surgidos através do Gestor de Recursos Azure.

## <a name="error-bad-request"></a>Erro: Mau pedido

"Mau Pedido" implica que o pedido recebido pelo Gestor de Recursos falhou na validação.

| Código de erro | SubCódes de Erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Pedido Incorreto | 40000 | SubCode=40000. The property *'property name'* cannot be set when creating a Queue because the namespace *'namespace name'* is using the 'Basic' Tier. Esta operação só é suportada no nível 'Standard' ou 'Premium'. | No Azure Service Bus Basic Tier, as propriedades abaixo não podem ser definidas ou atualizadas - <ul> <li> RequerDuplicateDetecção </li> <li> AutoDeleteOnIdle </li> <li>Requer sesessão</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Tópicos </li> </ul> | Considere atualização de Nível Básico para Standard ou Premium para utilizar esta funcionalidade. |
| Pedido Incorreto | 40000 | SubCode=40000. O valor para a propriedade 'requerDuplicateDetection' de uma fila (ou tópico) existente não pode ser alterado. | A deteção duplicada deve ser ativada/desativada no momento da criação da entidade. O parâmetro de configuração de deteção duplicado não pode ser alterado após a criação. | Para permitir a deteção duplicada numa fila/tópico previamente criado, pode criar uma nova fila/tópico com deteção duplicada e, em seguida, encaminhar da fila original para a nova fila/tópico. |
| Pedido Incorreto | 40000 | SubCode=40000. O valor especificado 16384 é inválido. A propriedade 'MaxSizeInMegabytes', deve ser um dos seguintes valores: 1024;2048;3072;4096;5120. | O valor MaxSizeInMegabytes é inválido. | Certifique-se de que o MaxSizeInMegabytes é um dos seguintes - 1024, 2048, 3072, 4096, 5120. |
| Pedido Incorreto | 40000 | SubCode=40000. A partilha não pode ser alterada para a fila/tópico. | A divisão não pode ser alterada para entidade. | Crie uma nova entidade (fila ou tópico) e permita divisórias. | 
| Pedido Incorreto | nenhuma | O nome *do "nome do espaço de nome"* não existe. | O espaço de nome não existe dentro da sua subscrição Azure. | Para resolver este erro, tente abaixo <ul> <li> Certifique-se de que a Assinatura Azure está correta. </li> <li> Certifique-se de que o espaço de nomes existe. </li> <li> Verifique se o nome do espaço de nome está correto (sem erros ortográficos ou cordas nulas). </li> </ul> | 
| Pedido Incorreto | 40400 | SubCode=40400. A entidade de destino de encaminhamento automático não existe. | O destino para a entidade de destino de auto-via não existe. | A entidade de destino (fila ou tópico), deve existir antes da criação da fonte. Releminar depois de criar a entidade de destino. |
| Pedido Incorreto | 40000 | SubCode=40000. O tempo de bloqueio fornecido excede o máximo permitido de '5' minutos. | O tempo para o qual uma mensagem pode ser bloqueada deve estar entre 1 minuto (mínimo) e 5 minutos (máximo). | Certifique-se de que o tempo de bloqueio fornecido é entre 1 min e 5 minutos. |
| Pedido Incorreto | 40000 | SubCode=40000. Tanto aPersistência Atrasada como a propriedade requerDuplicateDetection não podem ser ativadas em conjunto. | As entidades com deteção duplicada habilitada para as empresas devem ser persistentes, pelo que a persistência não pode ser retardada. | Saiba mais sobre [a Deteção de Duplicados](duplicate-detection.md) |
| Pedido Incorreto | 40000 | SubCode=40000. O valor para a propriedade RequerSession de uma fila existente não pode ser alterado. | O suporte às sessões deve ser ativado no momento da criação da entidade. Uma vez criado, não é possível ativar/desativar sessões numa entidade existente (fila ou subscrição) | Elimine e recrie uma nova fila (ou subscrição) com a propriedade "RequiresSession" ativada. |
| Pedido Incorreto | 40000 | SubCode=40000. 'URI_PATH' contém caracteres que não são permitidos pela Service Bus. Os segmentos de entidades podem conter apenas letras, números, períodos(.), hífens(-) e sublinhados(_). | Os segmentos de entidades podem conter apenas letras, números, períodos(.), hífens(-) e sublinhados(_). Quaisquer outros caracteres fazem com que o pedido falhe. | Certifique-se de que não existem caracteres inválidos no Caminho URI. |


## <a name="error-code-429"></a>Código de erro: 429

Tal como em HTTP, "Error code 429" indica "demasiados pedidos". Implica que o recurso específico (espaço de nome) está a ser estrangulado devido a demasiados pedidos (ou devido a operações conflituosas) nesse recurso.

| Código de erro | SubCódes de Erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | SubCode=50004. O pedido foi encerrado porque o espaço de nome *do seu espaço* está a ser acelerado. | Esta condição de erro é atingida quando o número de pedidos de entrada excede a limitação do recurso. | Espere alguns segundos e tente de novo. <br/> <br/> Saiba mais sobre as [quotas](service-bus-quotas.md) e [os limites de pedido do Gestor de Recursos Azure](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | SubDCode=40901. Outra operação conflituosa está em curso. | Outra operação conflituosa está em curso no mesmo recurso/entidade | Aguarde que a operação em curso esteja concluída antes de tentar novamente. |
| 429 | 40900 | SubCode=40900. Um conflito. Está a pedir uma operação que não é permitida no estado atual dos recursos. | Esta condição pode ser atingida quando são feitos vários pedidos para realizar as operações na mesma entidade (fila, tópico, subscrição ou regra) ao mesmo tempo. | Espere alguns segundos e tente de novo. |
| 429 | 40901 | Pedido de *entidade "nome de entidade"* entrou em conflito com outro pedido | Outra operação conflituosa está em curso no mesmo recurso/entidade | Aguarde que a operação anterior termine antes de tentar novamente. |
| 429 | 40901 | Está em curso um outro pedido de atualização para o nome da entidade *«nome da entidade».* | Outra operação conflituosa está em curso no mesmo recurso/entidade | Aguarde que a operação anterior termine antes de tentar novamente. |
| 429 | nenhuma | Conflito de recursos ocorreu. Outra operação conflituosa pode estar em curso. Se isto for uma repetição para uma operação falhada, a limpeza de antecedentes ainda está pendente. Tente novamente mais tarde. | Esta condição pode ser atingida quando houver uma operação pendente contra a mesma entidade. | Aguarde que a operação anterior esteja concluída antes de tentar novamente. |


## <a name="error-code-not-found"></a>Código de erro: Não encontrado

Esta classe de erros indica que o recurso não foi encontrado.

| Código de erro | SubCódes de Erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não encontrado | nenhuma | Não foi encontrado *o "nome da entidade".* | A entidade contra a qual a operação não foi encontrada. | Verifique se a entidade existe e tente a operação novamente. |
| Não encontrado | nenhuma | Não encontrado. A Operação não existe. | A operação que está a tentar realizar não existe. | Verifique a operação e tente de novo. |
| Não encontrado | nenhuma | O pedido de entrada não é reconhecido como um pedido de política de espaço de nome. | O órgão de pedido de entrada é nulo e, portanto, não pode ser executado como um pedido de amissão. | Verifique o corpo de pedido para se certificar de que não é nulo. | 
| Não encontrado | nenhuma | A entidade de mensagens *'nome da entidade'* não foi encontrada. | A entidade contra a quais está a tentar executar a operação não foi encontrada. | Verifique se a entidade existe e tente novamente a operação. |

## <a name="error-code-internal-server-error"></a>Código de erro: Erro interno do servidor

Esta classe de erros indica que houve um erro interno do servidor

| Código de erro | SubCódes de Erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | 50000 | SubCode=50000. Erro interno do servidor| Pode acontecer por várias razões. Alguns dos sintomas são... <ul> <li> O pedido/corpo do cliente é corrupto e leva a um erro. </li> <li> O pedido do cliente foi cronometrado devido a problemas de processamento no serviço. </li> </ul> | Para resolver isto <ul> <li> Certifique-se de que os parâmetros dos pedidos não são nulos ou mal formados. </li> <li> Recandidutar o pedido. </li> </ul> |

## <a name="error-code-unauthorized"></a>Código de erro: Não autorizado

Esta classe de erros indica a ausência de autorização para executar o comando.

| Código de erro | SubCódes de Erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não autorizado | nenhuma | Operação inválida no espaço de nome secundário. O espaço secundário é só para leitura. | A operação foi realizada contra o espaço de nome secundário, que é configurado como um espaço de nome readonly. | Recandidutar o comando contra o espaço de nome primário. Saiba mais sobre [o espaço de nome secundário](service-bus-geo-dr.md) |
| Não autorizado | nenhuma | DesaparecidoToken: O cabeçalho de autorização não foi encontrado. | Este erro ocorre quando a autorização tem valores nulos ou incorretos. | Certifique-se de que o valor simbólico mencionado no cabeçalho de autorização está correto e não nulo. |