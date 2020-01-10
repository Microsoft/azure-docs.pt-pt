---
title: Pré-requisitos do avere vFXT – Azure
description: Pré-requisitos para avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 27b0fd1e6a9238e978518e06ab7f0aeeae5501d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414394"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o vFXT Avere

Este artigo explica as tarefas de pré-requisito para criar um cluster avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

Comece criando uma nova assinatura do Azure. Use uma assinatura separada para cada projeto avere vFXT para permitir que você acompanhe facilmente todos os recursos e despesas do projeto, proteja outros projetos de uma possível limitação de recursos durante o provisionamento e simplifique a limpeza.

Para criar uma nova assinatura do Azure no portal do Azure:

* Navegue até a [folha assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Clique no botão **+ Adicionar** na parte superior
* Entrar, se solicitado
* Selecione uma oferta e percorra as etapas para criar uma nova assinatura

## <a name="configure-subscription-owner-permissions"></a>Configurar permissões de proprietário da assinatura

Um usuário com permissões de proprietário para a assinatura deve criar o cluster vFXT. As permissões de proprietário da assinatura são necessárias para aceitar os termos de serviço do software e executar outras ações.

Há alguns cenários de solução alternativa que permitem que um não proprietário crie um avere vFTX para o cluster do Azure. Esses cenários envolvem a restrição de recursos e a atribuição de funções adicionais ao criador. Em ambos os casos, um proprietário de assinatura também deve [aceitar os termos de software avere vFXT](#accept-software-terms) antes do tempo.

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster avere vFXT |
|----------|--------|-------|
| Administrador do grupo de recursos | A rede virtual, o controlador de cluster e os nós de cluster devem ser criados dentro do grupo de recursos | Funções de administrador e [colaborador](../role-based-access-control/built-in-roles.md#contributor) de [acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) , ambas com escopo para o grupo de recursos de destino |
| Rede virtual externa | O controlador de cluster e os nós de cluster são criados dentro do grupo de recursos, mas uma rede virtual existente em um grupo de recursos diferente é usada | (1) funções de administrador e [colaborador](../role-based-access-control/built-in-roles.md#contributor) de [acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) com escopo para o grupo de recursos vFXT; e (2) [colaborador de máquina virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [administrador de acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator)e funções de colaborador de [avere](../role-based-access-control/built-in-roles.md#avere-contributor) com escopo definido para o grupo de recursos da rede virtual. |

Uma alternativa é criar uma função de RBAC (controle de acesso baseado em função) personalizada antecipadamente e atribuir privilégios ao usuário, conforme explicado neste [artigo](avere-vfxt-non-owner.md). Esse método fornece permissões significativas para esses usuários.

## <a name="quota-for-the-vfxt-cluster"></a>Cota para o cluster vFXT

Você deve ter cota suficiente para os seguintes componentes do Azure. Se necessário, [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> As máquinas virtuais e os componentes SSD listados aqui são para o próprio cluster vFXT. Você precisará de mais cota para as VMs e SSD que pretende usar para seu farm de computação.  Verifique se a cota está habilitada para a região em que você pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Virtual Machines|3 ou mais E32s_v3|
|Armazenamento SSD Premium|200 GB de espaço para SO e 1 a 4 TB de espaço na cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contêiner de blob LRS |

## <a name="accept-software-terms"></a>Aceitar os termos de software

> [!NOTE]
> Esta etapa não será necessária se um proprietário de assinatura criar o cluster avere vFXT.

Durante a criação do cluster, você deve aceitar os termos de serviço do software avere vFXT. Se você não for um proprietário de assinatura, peça que o proprietário da assinatura aceite os termos antecipadamente. Essa etapa só precisa ser feita uma vez por assinatura.

Para aceitar os termos de software com antecedência:

1. Abra um Cloud Shell no portal do Azure ou navegando até <https://shell.azure.com>. Entre com sua ID de assinatura.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Emita este comando para aceitar os termos de serviço e habilitar o acesso programático para a imagem de software avere vFXT para Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Criar um ponto de extremidade de serviço de armazenamento em sua rede virtual (se necessário)

Um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego de BLOBs do Azure local, em vez de roteá-lo fora da rede virtual. É recomendável para qualquer vFXT avere para o cluster do Azure que usa o blob do Azure para armazenamento de dados de back-end.

Se você estiver fornecendo uma rede virtual existente e criando um novo contêiner de blob do Azure para o armazenamento de back-end como parte da criação do cluster, você deverá ter um ponto de extremidade de serviço na rede para o armazenamento da Microsoft. Esse ponto de extremidade deve existir antes da criação do cluster ou a criação falhará.

Um ponto de extremidade de serviço de armazenamento é recomendado para qualquer vFXT avere para o cluster do Azure que usa o armazenamento de BLOBs do Azure, mesmo que você adicione o armazenamento posteriormente.

> [!TIP]
>
>* Ignore esta etapa se você estiver criando uma nova rede virtual como parte da criação do cluster.
>* Essa etapa será opcional se você não estiver criando o armazenamento de blob durante a criação do cluster. Nesse caso, você poderá criar o ponto de extremidade de serviço posteriormente se decidir usar o blob do Azure.

Crie o ponto de extremidade do serviço de armazenamento no portal do Azure.

1. No portal, abra a lista de redes virtuais.
1. Selecione a rede virtual para o cluster.
1. Clique em **pontos de extremidade de serviço** no menu à esquerda.
1. Clique em **Adicionar** na parte superior.
1. Escolha o ``Microsoft.Storage``do serviço.
1. Selecione a sub-rede do cluster.
1. Na parte inferior, clique em **Adicionar**.

   ![portal do Azure captura de tela com anotações para as etapas de criação do ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>Próxima etapa: criar o cluster vFXT

Depois de concluir esses pré-requisitos, você pode ir para a criação do próprio cluster. Leia [implantar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.
