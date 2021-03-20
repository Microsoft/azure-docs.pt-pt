---
title: Especificar o grupo de recursos para VMs em Azure DevTest Labs | Microsoft Docs
description: Saiba como especificar um grupo de recursos para VMs num laboratório em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483368"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especificar um grupo de recursos para máquinas virtuais de laboratório em Azure DevTest Labs

Como dono de laboratório, pode configurar as suas máquinas virtuais de laboratório para serem criadas num grupo de recursos específico. Esta funcionalidade ajuda-o nos seguintes cenários:

- Tenha menos grupos de recursos criados por laboratórios na sua subscrição.
- Faça com que os seus laboratórios funcionem dentro de um conjunto fixo de grupos de recursos que configura.
- Trabalhe em torno de restrições e aprovações necessárias para criar grupos de recursos dentro da sua subscrição Azure.
- Consolidar todos os seus recursos de laboratório dentro de um único grupo de recursos para simplificar o acompanhamento desses recursos e aplicar [políticas](../governance/policy/overview.md) para gerir recursos a nível de grupo de recursos.

Com esta funcionalidade, pode utilizar um script para especificar um grupo de recursos novo ou existente dentro da subscrição do Azure para todos os seus VMs de laboratório. Atualmente, a Azure DevTest Labs suporta esta funcionalidade através de uma API.

> [!NOTE]
> Todos os limites de subscrição se aplicam quando cria laboratórios em Laboratórios DevTest. Pense num laboratório como qualquer outro recurso na sua assinatura. No caso de grupos de recursos, o limite é [de 980 grupos de recursos por subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Siga estes passos para especificar um grupo de recursos para todos os VMs criados no laboratório. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu de navegação à esquerda. 
3. Selecione **DevTest Labs** da lista.
4. Da lista de laboratórios, selecione o seu **laboratório.**  
5. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo. 
6. Selecione **as definições** de laboratório no menu esquerdo. 
7. Selecione **Todas as máquinas virtuais num único grupo de recursos.** 
8. Selecione um grupo de recursos existente na lista de drop-down (ou) **selecione Criar novo,** introduzir um **nome** para o grupo de recursos e selecionar **OK**. 

    ![Selecione o grupo de recursos para todos os VMs de laboratório](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Utilizar o PowerShell 
O exemplo a seguir mostra como usar um script PowerShell para criar todas as máquinas virtuais de laboratório num novo grupo de recursos.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Invoque o script utilizando o seguinte comando. ResourceGroup.ps1 é o ficheiro que contém o script anterior:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Use um modelo de gestor de recursos Azure
Se estiver a utilizar um modelo de Gestor de Recursos Azure para criar um laboratório, utilize a propriedade **vmCreationResourceGroupId** na secção de propriedades de laboratório do seu modelo, como mostra o seguinte exemplo:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API para configurar um grupo de recursos para VMs de laboratório
Tem as seguintes opções como proprietário de laboratório ao utilizar esta API:

- Escolha o **grupo de recursos do laboratório** para todas as máquinas virtuais.
- Escolha um **grupo de recursos existente** que não seja o grupo de recursos do laboratório para todas as máquinas virtuais.
- Introduza um novo nome **de grupo de recursos** para todas as máquinas virtuais.
- Continue a utilizar o comportamento existente, no qual é criado um grupo de recursos para cada VM no laboratório.
 
Esta definição aplica-se a novas máquinas virtuais criadas em laboratório. Os VMs mais antigos do seu laboratório que foram criados nos seus próprios grupos de recursos não são afetados. Os ambientes criados no seu laboratório continuam a permanecer nos seus próprios grupos de recursos.

Como utilizar esta API:
- Utilize a versão API **2018_10_15_preview**.
- Se especificar um novo grupo de recursos, certifique-se de que tem **permissões de escrita em grupos de recursos** na sua subscrição. Se não tiver permissões de escrita, a criação de novas máquinas virtuais no grupo de recursos especificado falhará.
- Durante a utilização da API, passe no **ID do grupo de recursos completo**. Por exemplo: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Certifique-se de que o grupo de recursos está na mesma subscrição que o laboratório. 


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-set-lab-policy.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
