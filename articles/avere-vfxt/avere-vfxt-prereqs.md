---
title: Pré-requisitos Avere vFXT - Azure
description: Saiba mais sobre tarefas a executar antes de criar um cluster em Avere vFXT para Azure, incluindo lidar com subscrições, quotas e pontos finais de serviço de armazenamento.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: d87f57873a4bb84b20df3da3880017d9ef2484a5
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342115"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o vFXT Avere

Este artigo explica tarefas pré-requisitos para a criação de um cluster Avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova subscrição

Comece por criar uma nova subscrição do Azure. Utilize uma subscrição separada para cada projeto Avere vFXT para simplificar o rastreio e limpeza de despesas, e para garantir que outros projetos não sejam afetados por quotas ou aceleração de recursos ao aprovisionar o fluxo de trabalho do cluster.

Para criar uma nova subscrição do Azure no portal Azure:

1. Navegue para a [lâmina de subscrições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Clique no botão **+ Adicionar** na parte superior
1. Inscreva-se se solicitado
1. Selecione uma oferta e caminhe pelos passos para criar uma nova subscrição

## <a name="configure-subscription-owner-permissions"></a>Configure permissões do proprietário da subscrição

Um utilizador com permissões do proprietário para a subscrição deve criar o cluster vFXT. A criação de clusters requer que um proprietário aceite os termos de serviço do software e autorize alterações nos recursos de rede e armazenamento.

Existem algumas soluções alternativas para permitir que um não proprietário crie um Avere vFXT para cluster Azure. Estes cenários envolvem restringir recursos e atribuir papéis adicionais de Azure ao criador. Em todos estes casos, um proprietário de subscrição também deve aceitar os termos de [software Avere vFXT](#accept-software-terms) antes do tempo.

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster Avere vFXT |
|----------|--------|-------|
| Administrador de grupo de recursos cria o vFXT | A rede virtual, o controlador de clusters e os nós de cluster devem ser criados dentro do grupo de recursos. | [Funções de Administrador de Acesso ao Utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) e [colaborador,](../role-based-access-control/built-in-roles.md#contributor) ambas com o âmbito do grupo de recursos-alvo. |
| Utilize uma rede virtual externa existente | O controlador de cluster e os nós de cluster são criados dentro do grupo de recursos vFXT, mas utilizam uma rede virtual existente num grupo de recursos diferente. | (1) [Funções de administrador](../role-based-access-control/built-in-roles.md#user-access-administrator) de acesso ao utilizador e [colaboradores](../role-based-access-control/built-in-roles.md#contributor) no âmbito do grupo de recursos vFXT; e (2) [Contribuinte de Máquinas Virtuais,](../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [Administrador de Acesso ao Utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator)e [Contribuidores Avere,](../role-based-access-control/built-in-roles.md#avere-contributor) com o âmbito do grupo de recursos da rede virtual. |
| Papel personalizado para criadores de clusters | Sem restrições de colocação de recursos. Este método confere aos não proprietários privilégios significativos. | O proprietário da subscrição cria um papel personalizado Azure, conforme explicado [neste artigo.](avere-vfxt-non-owner.md) |

## <a name="quota-for-the-vfxt-cluster"></a>Quota para o cluster vFXT

Verifique se tem quota suficiente para os seguintes componentes Azure. Se necessário, [solicite um aumento da quota.](../azure-portal/supportability/resource-manager-core-quotas-request.md)

> [!NOTE]
> As máquinas virtuais e os componentes SSD listados aqui são para o cluster vFXT em si. Lembre-se que também precisa de quota para os VMs e SSDs que vai usar para a sua quinta de computação.
>
> Certifique-se de que a quota está ativada para a região onde pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|3 ou mais E32s_v3 (um por nó de cluster) |
|Armazenamento SSD Premium|200 GB de espaço para SO e 1 a 4 TB de espaço na cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo recipiente LRS Blob |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Aceitar termos de software

> [!TIP]
> Salte este passo se um proprietário de subscrição criar o cluster Avere vFXT.

Durante a criação do cluster, você deve aceitar os termos de serviço para o software Avere vFXT. Se não for proprietário de subscrição, mandem um proprietário de subscrição a aceitar os termos com antecedência.

Este passo só precisa de ser feito uma vez por subscrição.

Para aceitar os termos do software com antecedência:

1. Abra uma casca de nuvem no portal Azure ou navegando para <https://shell.azure.com> . Inscreva-se com o seu ID de assinatura.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Emita este comando para aceitar termos de serviço e permitir o acesso programático para a imagem do software Avere vFXT para Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Crie um ponto final de serviço de armazenamento na sua rede virtual (se necessário)

Um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego Azure Blob local em vez de encaminhá-lo para fora da rede virtual. Recomenda-se para qualquer avere vFXT para cluster Azure que utilize a Azure Blob para armazenamento de dados de back-end.

Se criar uma nova rede virtual enquanto cria o cluster, um ponto final é criado automaticamente. Se fornecer uma rede virtual existente, deve ter um ponto final de serviço de armazenamento da Microsoft se quiser criar um novo recipiente de armazenamento Blob durante a criação do cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Ignore este passo se estiver a criar uma nova rede virtual como parte da criação de clusters.
>* Um ponto final é opcional se não estiver a criar armazenamento Blob durante a criação do cluster. Nesse caso, pode criar o ponto final de serviço mais tarde se decidir utilizar a Azure Blob.

Crie o ponto final do serviço de armazenamento a partir do portal Azure.

1. No portal, abra a sua lista de redes virtuais.
1. Selecione a rede virtual para o seu cluster.
1. Clique **nos pontos finais** do serviço a partir do menu esquerdo.
1. Clique em **Adicionar** na parte superior.
1. Escolha o ``Microsoft.Storage`` serviço.
1. Selecione a sub-rede do cluster.
1. Na parte inferior, clique **em Adicionar**.

   ![Screenshot do portal Azure com anotações para os passos de criação do ponto final de serviço](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Passos seguintes

Depois de completar estes pré-requisitos, pode criar o cluster. Leia [O conjunto vFXT](avere-vfxt-deploy.md) para obter instruções.