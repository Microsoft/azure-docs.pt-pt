---
title: Mover recursos associados a uma configuração de manutenção para outra região
description: Saiba como mover recursos associados a uma configuração de manutenção VM para outra região do Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304449"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Mover recursos numa configuração de Controlo de Manutenção para outra região

Siga este artigo para mover recursos associados a uma configuração de Controlo de Manutenção para uma região azure diferente. Talvez queira mover uma configuração por uma série de razões. Por exemplo, aproveitar uma nova região, implantar funcionalidades ou serviços disponíveis numa região específica, satisfazer os requisitos de política interna e governação, ou em resposta ao planeamento de capacidades.

O controlo de manutenção, com configurações de manutenção personalizadas, permite controlar a forma como as atualizações da plataforma são aplicadas aos VMs [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) e [Linux,](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) e aos Anfitriões Dedicados do Azure. Existem alguns cenários para mover o controlo de manutenção em todas as regiões:

- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga este artigo.
- Para mover a configuração do seu controlo de manutenção, mas não os recursos associados à configuração, siga [estas instruções](move-region-maintenance-configuration.md).
- Para mover tanto a configuração de manutenção como os recursos associados, siga primeiro [estas instruções](move-region-maintenance-configuration.md). Em seguida, siga as instruções neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover os recursos associados a uma configuração de Controlo de Manutenção:

- Certifique-se de que os recursos que está a mover existem na nova região antes de começar.
- Verifique as configurações de Controlo de Manutenção associadas aos VMs Azure e aos Anfitriões Dedicados Azure que pretende mover. Verifique cada recurso individualmente. Não há forma de recuperar configurações para vários recursos.
- Ao recuperar configurações para um recurso:
    - Certifique-se de que utiliza o ID de subscrição para a conta e não um ID de anfitrião dedicado azure.
    - CLI: O parâmetro de tabela -saída é utilizado apenas para a legibilidade, e pode ser eliminado ou alterado.
    - PowerShell: O parâmetro nome da tabela de formato é utilizado apenas para a legibilidade e pode ser eliminado ou alterado.
    - Se utilizar o PowerShell, obtém um erro se tentar listar configurações para um recurso que não tenha configurações associadas. O erro será semelhante ao seguinte: "Operação falhada com estatuto: 'Não Encontrado'. Detalhes: 404 Erro do Cliente: Não encontrado para url".

    
## <a name="prepare-to-move"></a>Prepare-se para se mover

1. Antes de começar, defina estas variáveis. Nós fornecemos um exemplo para cada um.

    **Variável** | **Detalhes** | **Exemplo**
    --- | ---
    $subId | ID para subscrição que contenha as configurações de manutenção | "o nosso iD de subscrição"
    $rsrcGroupName | Nome do grupo de recursos (Azure VM) | "VMResourceGroup"
    $vmName | Nome de recurso VM |  "myVM"
    $adhRsrcGroupName |  Grupo de recursos (anfitriões dedicados) | "HostResourceGroup"
    $adh | Nome dedicado do anfitrião | "MyHost"
    $adhParentName | Nome de recurso-mãe | "HostGroup"
    
2. Para recuperar as configurações de manutenção utilizando o comando PowerShell [Get-AZConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - Para anfitriões dedicados azure, corra:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Para Os VMs Azure, corra:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Para recuperar as configurações de manutenção utilizando o comando de atribuição de manutenção CLI [az:](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)

    - Para anfitriões dedicados azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Para VMs Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Mover 

1. [Siga estas instruções](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) para mover os VMs Azure para a nova região.
2. Depois de movidos os recursos, reaplique as configurações de manutenção aos recursos da nova região conforme apropriado, dependendo se mudou as configurações de manutenção. Pode aplicar uma configuração de manutenção a um recurso utilizando [powerShell](../virtual-machines/maintenance-control-powershell.md) ou [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Verifique o movimento

Verifique os recursos na nova região e verifique as configurações associadas aos recursos da nova região. 

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Após a mudança, considere apagar os recursos movidos na região de origem.


## <a name="next-steps"></a>Passos seguintes

Siga [estas instruções](move-region-maintenance-configuration.md) se precisar de mover as configurações de manutenção. 
