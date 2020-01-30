---
title: Modelo de implantação clássico do Azure
description: O modelo de implantação Classic, agora substituído pelo modelo Degestor de Recursos, impõe um limite global de quota vCPU para VMs e conjuntos de escala de máquinas virtuais.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76835634"
---
# <a name="classic-deployment-model"></a>Modelo de implementação clássica

O modelo de implantação clássico é o modelo de implantação do Azure de geração mais antiga. Ele impõe um limite de cota de vCPU global para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. O modelo de implantação clássico não é mais recomendado e agora é substituído pelo modelo do Resource Manager.

Para saber mais sobre estes dois modelos de implementação e as vantagens de usar o Gestor de Recursos, consulte O Gestor de Recursos e a [implantação clássica.](../../azure-resource-manager/management/deployment-models.md)

Quando uma nova assinatura é criada, uma cota padrão de vCPUs é atribuída a ela. Sempre que uma nova máquina virtual deve ser implantada utilizando o modelo de implantação Classic, a soma da utilização vCPU nova e existente em todas as regiões não deve exceder a quota vCPU aprovada para o modelo de implantação Classic.

Para saber mais sobre quotas, consulte [limites de subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Pode solicitar um aumento do limite de quota vCPU para o modelo de implantação Classic. Utilize ajuda **+ suporte** ou **utilização + quotas** no portal Azure.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Pedido por VM série vCPU aumento de quota ao nível de subscrição usando Ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte usando **ajuda + suporte** no portal do Azure.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![Selecione Ajuda + suporte no portal Azure](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecione **Novo pedido de suporte**.

   ![Criar um novo pedido de apoio no portal Azure](./media/resource-manager-core-quotas-request/new-support-request.png)

1. No **tipo emissão,** escolha limites de **serviço e subscrição (quotas)** .

   ![Selecione quotas como o tipo de emissão](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione subscrição para a qual aumentar uma quota](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o **tipo quota,** selecione O limite de **subscrição Compute -VM (cores-vCPUs) aumenta**.

   ![Selecione o tipo de quota para aumentar](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Selecione **Fornecer detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para ajudar o seu pedido](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes de Quota,** selecione **Classic** e selecione um **Local**.

   ![Adicione detalhes, incluindo o modelo de implantação e localização](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Para a **família SKU,** selecione uma ou mais famílias SKU para aumentar.

   ![Especifique a família SKU para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque o SKU da **família SKU** ou selecione o ícone "X" de devoluções. Depois de introduzir uma quota para cada família SKU, selecione **Guardar e Continuar** em dados de **Quota** para continuar com o pedido de apoio.

   ![Solicitar novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Pedido por vM série vCPU quota aumentar ao nível de subscrição usando utilização + quotas

Siga as instruções abaixo para criar um pedido de apoio utilizando **o Uso + quotas** no portal Azure.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas.**

   ![Ir a Assinaturas no portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione subscrição para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecione **uso + cotas**.

   ![Selecione utilização e quotas para uma subscrição](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecione O limite de **subscrição Compute-VM (cores-vCPUs) aumenta** à medida que o **tipo de quota**.

   ![Selecionar tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Selecione **Fornecer detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para o seu pedido](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes de Quota**, selecione **Classic** e a **Location**.

   ![Selecione detalhes de quota, incluindo modelo de implementação e localização](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecione uma ou mais famílias SKU para um aumento.

   ![Selecione família SKU para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque o SKU da **família SKU** ou selecione o ícone "X" de devoluções. Depois de introduzir uma quota para cada família SKU, selecione **Guardar e Continuar** em dados de **Quota** para continuar com o pedido de apoio.

   ![Insira nova quota](./media/resource-manager-core-quotas-request/new-limits-classic.png)

