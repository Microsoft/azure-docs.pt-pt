---
title: Especifique o grupo de recursos para as VMs no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como especificar um grupo de recursos para as VMs num laboratório no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: e4e2a01bbac7aebb70852b93c51c32933cc75eec
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652183"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especifique um grupo de recursos para máquinas virtuais do laboratório no Azure DevTest Labs

Como proprietário de um laboratório, pode configurar as máquinas virtuais do laboratório ser criado num grupo de recursos específico. Esta funcionalidade ajuda-o nos seguintes cenários:

- Tiver menos grupos de recursos criados por laboratórios na sua subscrição.
- Ter os laboratórios de operar dentro de um conjunto fixo de grupos de recursos que configurar.
- Contornar as restrições e aprovações requeridas para a criação de grupos de recursos na sua subscrição do Azure.
- Consolidar todos os recursos do laboratório dentro de um grupo de recursos único para simplificar a controlar esses recursos e aplicar [políticas](../governance/policy/overview.md) para gerir os recursos ao nível do grupo de recursos.

Com esta funcionalidade, pode utilizar um script para especificar um grupo de recursos novo ou existente na sua subscrição do Azure para todos os seu laboratório VMs. Atualmente, o Azure DevTest Labs suporta esta funcionalidade através de uma API.

## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API para configurar um grupo de recursos para as VMs do laboratório
Tem as seguintes opções como proprietário de um laboratório ao utilizar esta API:

- Escolha o **grupo de recursos do laboratório** todas as máquinas virtuais.
- Escolher uma **grupo de recursos existente** que não seja o grupo de recursos do laboratório para todas as máquinas virtuais.
- Introduza um **novo grupo de recursos** nome todas as máquinas virtuais.
- Continue a utilizar o comportamento existente, no qual um grupo de recursos é criado para cada VM no laboratório.
 
Esta definição aplica-se para novas máquinas de virtuais criadas no laboratório. As VMs mais antigas em seu laboratório que foram criadas em seus próprios grupos de recursos não são afetadas. Ambientes que são criados no seu laboratório continuam a permanecer em seus próprios grupos de recursos.

Como utilizar esta API:
- Versão de API de utilização **2018_10_15_preview**.
- Se especificar um novo grupo de recursos, certifique-se de que tenha **permissões de escrita em grupos de recursos** na sua subscrição. Se não tem permissões de escrita, criação de novas máquinas virtuais no grupo de recursos especificado falhará.
- Ao utilizar a API, passar o **completa de ID do grupo de recursos**. Por exemplo: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Certifique-se de que o grupo de recursos está na mesma subscrição, como o laboratório. 

## <a name="use-powershell"></a>Utilizar o PowerShell 
O exemplo seguinte mostra como utilizar um script do PowerShell para criar todas as máquinas virtuais do laboratório num novo grupo de recursos.

```PowerShell
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

Invoca o script com o comando seguinte. ResourceGroup.ps1 é o ficheiro que contém o script anterior:

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Utilizar um modelo Azure Resource Manager
Se estiver a utilizar um modelo Azure Resource Manager para criar um laboratório, utilize o **vmCreationResourceGroupId** na seção de propriedades de laboratório do seu modelo, conforme mostrado no exemplo a seguir:

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


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
