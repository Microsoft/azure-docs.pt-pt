---
title: Avere vFXT pré-requisitos - Azure
description: Pré-requisitos para Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409265"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o vFXT Avere

Este artigo explica as tarefas de pré-requisitos para a criação de um cluster de vFXT Avere.

## <a name="create-a-new-subscription"></a>Criar uma nova subscrição

Comece por criar uma nova subscrição do Azure. Utilize uma subscrição separada para cada projeto de vFXT Avere para permitem-lhe controlar todos os recursos de projeto e despesas facilmente, proteger os outros projetos de limitação durante o aprovisionamento de recursos possível e simplificar a limpeza.  

Para criar uma nova subscrição do Azure no portal do Azure:

* Navegue para o [painel de subscrições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Clique nas **+ adicionar** botão na parte superior
* Iniciar sessão, se lhe for pedido
* Selecionar uma oferta e siga os passos para criar uma nova subscrição

## <a name="configure-subscription-owner-permissions"></a>Configurar permissões de proprietário da subscrição

Um utilizador com permissões de proprietário da subscrição deve criar o cluster vFXT. São necessárias permissões de proprietário de subscrição para aceitar os termos de software de serviço e efetuar outras ações. 

Existem alguns cenários de solução que permitem que um sem proprietário criar um vFTX Avere para cluster do Azure. Estes cenários envolvem a restrição de recursos e a atribuição de funções adicionais para o criador. Em ambos os casos, um proprietário da subscrição tem também [aceite os termos de software de vFXT Avere](#accept-software-terms) antes do tempo. 

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster de vFXT Avere | 
|----------|--------|-------|
| Administrador do grupo de recursos | A rede virtual, o controlador de cluster e nós de cluster tem de ser criados dentro do grupo de recursos | [Administrador de acesso de utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) e [contribuinte](../role-based-access-control/built-in-roles.md#contributor) funções, ambos âmbito para o grupo de recursos de destino | 
| Vnet externo | O controlador de cluster e nós de cluster são criados no grupo de recursos, mas é utilizada uma rede virtual existente no grupo de recursos diferente | (1) [administrador de acesso de utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) e [contribuinte](../role-based-access-control/built-in-roles.md#contributor) funções no âmbito de grupo de recursos vFXT; e (2) [contribuinte de Máquina Virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [acesso de utilizador Administrador](../role-based-access-control/built-in-roles.md#user-access-administrator), e [Avere contribuinte](../role-based-access-control/built-in-roles.md#avere-contributor) funções no âmbito do grupo de recursos da VNET. |
 
Uma alternativa é criar uma função de controlo (RBAC) de acesso baseado em funções personalizados antes do tempo e atribuir privilégios ao usuário, conforme explicado [este artigo](avere-vfxt-non-owner.md). Este método fornece permissões significativas a estes utilizadores. 

## <a name="quota-for-the-vfxt-cluster"></a>Quota para o cluster vFXT

Tem de ter uma quota suficiente para os seguintes componentes do Azure. Se for necessário, [pedir um aumento de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> As máquinas virtuais e os componentes SSD listados aqui são para o próprio cluster vFXT. Terá de quota adicional para as VMs e SSD que tenciona utilizar para o farm de computação.  Certifique-se de que a quota estiver ativada para a região onde pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|E32s_v3 3 ou mais|
|Armazenamento SSD Premium|200 GB de espaço para SO e 1 a 4 TB de espaço na cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contentor de BLOBs LRS |

## <a name="accept-software-terms"></a>Aceite os termos de software

> [!NOTE] 
> Este passo não é necessário se o proprietário da subscrição cria o cluster de vFXT Avere.

Durante a criação do cluster, tem de aceitar os termos de serviço para o software de vFXT Avere. Se não for um proprietário da subscrição, tem um proprietário da subscrição de aceitar os termos antes do tempo. Este passo só precisa ser feito uma vez por subscrição.

Para aceitar o software de termos com antecedência: 

1. Abra uma shell de cloud no portal do Azure ou ao navegar para <https://shell.azure.com>. Inicie sessão com o ID da subscrição.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Emita este comando para aceitar os termos de serviço e ativar o acesso programático a vFXT Avere para a imagem de software do Azure: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Criar um ponto de extremidade do serviço de armazenamento na sua rede virtual (se necessário)

R [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego de Blobs do Azure local em vez de encaminhá-lo fora da rede virtual. Recomenda-se para qualquer vFXT Avere para cluster do Azure que utiliza o Blob do Azure para o armazenamento de dados back-end. 

Se está a fornecer uma vnet já existente e criar um novo contentor de Blobs do Azure para o armazenamento de back-end como parte da criação do cluster, tem de ter um ponto de extremidade de serviço na vnet para o armazenamento do Microsoft. Este ponto final tem de existir antes de criar o cluster ou a criação falhará. 

Um ponto de extremidade do serviço de armazenamento é recomendado para qualquer vFXT Avere para cluster do Azure que utiliza o armazenamento de Blobs do Azure, mesmo que adicione o armazenamento mais tarde. 

> [!TIP] 
> * Ignore este passo se estiver a criar uma nova rede virtual como parte da criação do cluster. 
> * Este passo é opcional se não estiver a criar o armazenamento de BLOBs durante a criação do cluster. Nesse caso, pode criar o ponto final de serviço mais tarde se decidir utilizar BLOBs do Azure.

Crie o ponto de extremidade do serviço de armazenamento a partir do portal do Azure. 

1. No portal, clique em **redes virtuais** à esquerda.
1. Selecione a vnet para o seu cluster. 
1. Clique em **pontos finais de serviço** à esquerda.
1. Clique em **adicionar** na parte superior.
1. Deixe o serviço como ``Microsoft.Storage`` e selecione a sub-rede do cluster.
1. Na parte inferior, clique em **adicionar**.

   ![Captura de ecrã portal do Azure com anotações para obter os passos de criação de ponto final do serviço](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Passo seguinte: Criar o cluster vFXT

Depois de concluir estes pré-requisitos, pode saltar para criar o cluster em si. Leia [implementar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.