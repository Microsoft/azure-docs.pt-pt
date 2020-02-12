---
title: Especificar grupo de recursos para VMs em Azure DevTest Labs  Microsoft Docs
description: Aprenda a especificar um grupo de recursos para VMs num laboratório em Laboratórios Azure DevTest.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134538"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especifique um grupo de recursos para máquinas virtuais de laboratório em Laboratórios Azure DevTest

Como dono de laboratório, pode configurar as suas máquinas virtuais de laboratório para serem criadas num grupo de recursos específicos. Esta funcionalidade ajuda-o nos seguintes cenários:

- Tenha menos grupos de recursos criados por laboratórios na sua subscrição.
- Faça com que os seus laboratórios funcionem dentro de um conjunto fixo de grupos de recursos que configura.
- Trabalhe em torno de restrições e aprovações necessárias para a criação de grupos de recursos dentro da sua subscrição Azure.
- Consolide todos os seus recursos laboratoriais dentro de um único grupo de recursos para simplificar o acompanhamento desses recursos e aplicar [políticas](../governance/policy/overview.md) para gerir recursos ao nível do grupo de recursos.

Com esta funcionalidade, pode utilizar um script para especificar um novo ou existente grupo de recursos dentro da sua subscrição Azure para todos os seus VMs de laboratório. Atualmente, a Azure DevTest Labs suporta esta funcionalidade através de uma API.

> [!NOTE]
> Todos os limites de subscrição se aplicam quando se criam laboratórios em Laboratórios DevTest. Pense num laboratório como qualquer outro recurso na sua subscrição. No caso de grupos de recursos, o limite é [de 980 grupos de recursos por subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Siga estes passos para especificar um grupo de recursos para todos os VMs criados no laboratório. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu de navegação à esquerda. 
3. Selecione **DevTest Labs** da lista.
4. Da lista de laboratórios, selecione o seu **laboratório.**  
5. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo. 
6. Selecione **as definições** do Laboratório no menu esquerdo. 
7. Selecione **todas as máquinas virtuais num só grupo de recursos**. 
8. Selecione um grupo de recursos existente na lista de drop-down (ou) selecione **Criar novo**, insira um **nome** para o grupo de recursos e selecione **OK**. 

    ![Selecione o grupo de recursos para todos os VMs de laboratório](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Utilizar o PowerShell 
O exemplo que se segue mostra como usar um script PowerShell para criar todas as máquinas virtuais de laboratório num novo grupo de recursos.

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

## <a name="use-an-azure-resource-manager-template"></a>Use um modelo de Gestor de Recursos Azure
Se estiver a usar um modelo de Gestor de Recursos Azure para criar um laboratório, utilize a propriedade **vmCreationResourceGroupId** na secção de propriedades do laboratório do seu modelo, como mostra o seguinte exemplo:

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
Tem as seguintes opções como dono de laboratório ao utilizar esta API:

- Escolha o **grupo de recursos do laboratório** para todas as máquinas virtuais.
- Escolha um grupo de **recursos existente** que não o grupo de recursos do laboratório para todas as máquinas virtuais.
- Introduza um novo nome de grupo de **recursos** para todas as máquinas virtuais.
- Continue a usar o comportamento existente, no qual um grupo de recursos é criado para cada VM no laboratório.
 
Esta definição aplica-se a novas máquinas virtuais criadas em laboratório. Os VMs mais antigos do seu laboratório que foram criados nos seus próprios grupos de recursos permanecem inalterados. Os ambientes criados no seu laboratório continuam a permanecer nos seus próprios grupos de recursos.

Como utilizar esta API:
- Utilize a versão API **2018_10_15_preview**.
- Se especificar um novo grupo de recursos, certifique-se de que tem **permissões de escrita em grupos** de recursos na sua subscrição. Se não tiver permissões de escrita, a criação de novas máquinas virtuais no grupo de recursos especificado falhará.
- Ao utilizar a API, passe o ID do grupo de **recursos completo**. Por exemplo: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Certifique-se de que o grupo de recursos está na mesma subscrição que o laboratório. 


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
