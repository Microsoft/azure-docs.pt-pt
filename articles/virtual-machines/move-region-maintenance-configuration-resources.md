---
title: Mover recursos associados a uma configuração de manutenção para outra região
description: Saiba como mover recursos associados a uma configuração de manutenção VM para outra região do Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865248"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Mover recursos numa configuração de Controlo de Manutenção para outra região

Siga este artigo para mover recursos associados a uma configuração de Controlo de Manutenção para uma região Azure diferente. É melhor mover uma configuração por várias razões. Por exemplo, aproveitar uma nova região, implantar características ou serviços disponíveis numa região específica, satisfazer os requisitos de política interna e de governação, ou em resposta ao planeamento de capacidades.

[O controlo de manutenção,](maintenance-control.md)com configurações de manutenção personalizadas, permite-lhe controlar a forma como as atualizações da plataforma são aplicadas em VMs e em Azure Dedicated Hosts. Existem alguns cenários para mover o controlo de manutenção em todas as regiões:

- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga este artigo.
- Para mover a configuração do controlo de manutenção, mas não os recursos associados à configuração, siga [estas instruções](move-region-maintenance-configuration.md).
- Para mover tanto a configuração de manutenção como os recursos que lhe estão [associados,](move-region-maintenance-configuration.md)siga primeiro estas instruções . Em seguida, siga as instruções deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover os recursos associados a uma configuração de Controlo de Manutenção:

- Certifique-se de que os recursos que está a mover existem na nova região antes de começar.
- Verifique as configurações de Controlo de Manutenção associadas aos VMs Azure e aos Anfitriões Dedicados Azure que pretende mover. Verifique cada recurso individualmente. Não há forma de recuperar configurações para múltiplos recursos.
- Ao recuperar configurações para um recurso:
    - Certifique-se de que utiliza o ID de subscrição para a conta, não um ID do anfitrião dedicado Azure.
    - CLI: O parâmetro da tabela de saída é utilizado apenas para a legibilidade e pode ser eliminado ou alterado.
    - PowerShell: O parâmetro Format-Table Nome é utilizado apenas para a legibilidade e pode ser eliminado ou alterado.
    - Se utilizar o PowerShell, obtém-se um erro se tentar listar configurações para um recurso que não tenha configurações associadas. O erro será semelhante ao seguinte: "Operação falhada com estatuto: 'Não Encontrado'. Detalhes: 404 Erro do Cliente: Não encontrado para url".

    
## <a name="prepare-to-move"></a>Preparar para mover

1. Antes de começar, defina estas variáveis. Demos um exemplo para cada um.

    **Variável** | **Detalhes** | **Exemplo**
    --- | ---
    $subId | ID para subscrição que contenha as configurações de manutenção | "o nosso ID de subscrição"
    $rsrcGroupName | Nome do grupo de recursos (Azure VM) | "VMResourceGroup"
    $vmName | Nome de recurso VM |  "myVM"
    $adhRsrcGroupName |  Grupo de recursos (anfitriões dedicados) | "HostResourceGroup"
    $adh | Nome de anfitrião dedicado | "myHost"
    $adhParentName | Nome do recurso-mãe | "HostGroup"
    
2. Para recuperar as configurações de manutenção utilizando o comando PowerShell [Get-AZConfigurationAssignment:](/powershell/module/az.maintenance/get-azconfigurationassignment)

    - Para anfitriões dedicados Azure, corra:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Para os VMs Azure, corra:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Para recuperar as configurações de manutenção utilizando o comando de atribuição de manutenção CLI [az:](/cli/azure/maintenance/assignment)

    - Para anfitriões dedicados Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Para VMs Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Mover 

1. [Siga estas instruções](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) para mover os VMs Azure para a nova região.
2. Após a deslocação dos recursos, reencaminha as configurações de manutenção para os recursos da nova região, consoante tenha mudado as configurações de manutenção. Pode aplicar uma configuração de manutenção a um recurso utilizando [o PowerShell](../virtual-machines/maintenance-control-powershell.md) ou [o CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Verifique o movimento

Verifique os recursos na nova região e verifique as configurações associadas aos recursos na nova região. 

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Após a mudança, considere apagar os recursos movidos na região origem.


## <a name="next-steps"></a>Passos seguintes

Siga [estas instruções](move-region-maintenance-configuration.md) se precisar de mover as configurações de manutenção. 
