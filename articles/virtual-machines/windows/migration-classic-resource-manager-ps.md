---
title: Migrar para Gestor de Recursos com PowerShell
description: Este artigo percorre a migração apoiada pela plataforma de recursos IaaS, tais como máquinas virtuais (VMs), redes virtuais e contas de armazenamento do clássico para o Gestor de Recursos Azure, utilizando comandos Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 12a77c4c21a26f1ec52bb3ffdc312df56d3c4537
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396025"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrar recursos IaaS do clássico para o Gestor de Recursos Azure usando powerShell

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs iaas estão usando O Gestor de [Recursos Azure.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VMs clássicos foram depreciados e serão totalmente aposentados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Estes passos mostram-lhe como usar os comandos Azure PowerShell para migrar a infraestrutura como um serviço (IaaS) recursos do modelo clássico de implantação para o modelo de implantação do Gestor de Recursos Azure.

Se quiser, também pode migrar recursos utilizando o [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração apoiados pela Plataforma, consulte [a migração apoiada pela Plataforma de recursos IaaS do clássico para](migration-classic-resource-manager-overview.md)o Gestor de Recursos Azure.
* Para obter orientações detalhadas e um passeio migratório, consulte [o mergulho técnico profundo na migração apoiada pela plataforma do clássico para o Gestor de Recursos Azure.](migration-classic-resource-manager-deep-dive.md)
* [Reveja os erros de migração mais comuns.](migration-classic-resource-manager-errors.md)

<br>
Aqui está um fluxograma para identificar a ordem em que os passos precisam ser executados durante um processo de migração.

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Passo 1: Plano para a migração
Aqui estão algumas boas práticas que recomendamos enquanto avalia se deve migrar os recursos IaaS do clássico para o Gestor de Recursos:

* Leia através das [funcionalidades e configurações suportadas e não suportadas](migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizem configurações ou funcionalidades não suportadas, aguarde a configuração ou suporte de funcionalidades para ser anunciado. Em alternativa, se se adequar às suas necessidades, remova essa funcionalidade ou saia dessa configuração para permitir a migração.
* Se tem scripts automatizados que implementam a sua infraestrutura e aplicações hoje em dia, tente criar uma configuração de teste semelhante utilizando esses scripts para migração. Em alternativa, pode configurar ambientes de amostra utilizando o portal Azure.

> [!IMPORTANT]
> Os gateways de aplicação não são atualmente suportados para a migração do clássico para o Gestor de Recursos. Para migrar uma rede virtual com um portal de aplicação, remova o portal antes de executar uma operação Prepare para mover a rede. Depois de completar a migração, religue a porta de entrada no Azure Resource Manager.
>
> Os gateways Azure ExpressRoute que se ligam aos circuitos ExpressRoute numa outra subscrição não podem ser migrados automaticamente. Nesses casos, remova o gateway ExpressRoute, migra a rede virtual e recrie o portal. Para mais informações, consulte [os circuitos Migrate ExpressRoute e redes virtuais associadas do clássico ao modelo](../../expressroute/expressroute-migration-classic-resource-manager.md)de implementação do Gestor de Recursos.

## <a name="step-2-install-the-latest-version-of-powershell"></a>Passo 2: Instale a versão mais recente da PowerShell
Existem duas opções principais para instalar o Azure PowerShell: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). O WebPI recebe atualizações mensais. A PowerShell Gallery recebe atualizações de forma contínua. Este artigo baseia-se na versão 2.1.0 da Azure PowerShell.

Para instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Passo 3: Certifique-se de que é administrador para a subscrição
Para realizar esta migração, deve ser adicionado como coadministrador para a subscrição no [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **Hub,** **selecione Subscrição**. Se não o vir, selecione **Todos os serviços.**
3. Encontre a entrada de subscrição apropriada e, em seguida, olhe para o campo **MY ROLE.** Para um coadministrador, o valor deve ser _administração da Conta_.

Se não conseguir adicionar um coadministrador, contacte um administrador de serviço ou coadministrador para que a subscrição seja adicionada.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passo 4: Desloque a sua subscrição e inscreva-se para a migração
Primeiro, inicie uma solicitação powerShell. Para a migração, instale o seu ambiente tanto para o clássico como para o Gestor de Recursos.

Inscreva-se na sua conta para o modelo De Gestor de Recursos.

```powershell
    Connect-AzAccount
```

Obtenha as subscrições disponíveis utilizando o seguinte comando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Detete a sua assinatura Azure para a sessão atual. Este exemplo define o nome de subscrição predefinido para **a Subscrição My Azure**. Substitua o nome de subscrição de exemplo pelo seu.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registo é um passo único, mas deve fazê-lo uma vez antes de tentar a migração. Sem se registar, vê a seguinte mensagem de erro:
>
> *BadRequest : A subscrição não está registada para migração.*

Registe-se junto do fornecedor de recursos migratórios utilizando o seguinte comando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Espere cinco minutos para a inscrição terminar. Verifique o estado da aprovação utilizando o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Certifique-se de que o Estado de Registo está `Registered` antes de prosseguir.

Antes de mudar para o modelo de implementação clássico, certifique-se de que tem vCPUs de máquina virtual do Azure Resource Manager na região Azure da sua atual implantação ou rede virtual. Pode utilizar o seguinte comando PowerShell para verificar o número atual de vCPUs que tem no Gestor de Recursos Azure. Para saber mais sobre as quotas vCPU, consulte [Limits e o Gestor de Recursos Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)

Este exemplo verifica a disponibilidade na região **dos EUA Ocidentais.** Substitua o nome da região de exemplo pelo seu.

```powershell
    Get-AzVMUsage -Location "West US"
```

Agora, inscreva-se na sua conta para o modelo clássico de implantação.

```powershell
    Add-AzureAccount
```

Obtenha as subscrições disponíveis utilizando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Detete a sua assinatura Azure para a sessão atual. Este exemplo define a subscrição padrão da **Assinatura My Azure.** Substitua o nome de subscrição de exemplo pelo seu.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Passo 5: Executar comandos para migrar os seus recursos IaaS
* [Migram VMs em um serviço de nuvem (não em uma rede virtual)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migram VMs em uma rede virtual](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar uma conta de armazenamento](#step-52-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se tiver um problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que tente novamente a preparação, abortar ou comprometer o funcionamento. A plataforma tenta novamente a ação.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passo 5.1: Opção 1 - Migrar máquinas virtuais num serviço de nuvem (não numa rede virtual)
Obtenha a lista de serviços na nuvem utilizando o seguinte comando. Então escolha o serviço de nuvem que quer migrar. Se os VMs do serviço de nuvem estiverem numa rede virtual ou se tiverem funções web ou trabalhadores, o comando devolve uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome de implantação para o serviço de nuvem. Neste exemplo, o nome de serviço é **My Service**. Substitua o nome de serviço de exemplo com o seu próprio nome de serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Tem duas opções para escolher.

* **Opção 1: Migrar os VMs para uma rede virtual criada pela plataforma.**

    Primeiro, valide que pode migrar o serviço de nuvem utilizando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando seguinte apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode passar ao passo Prepare.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2: Migrar para uma rede virtual existente no modelo de implementação do Gestor de Recursos.**

    Este exemplo define o nome do grupo de recursos para **myResourceGroup,** o nome de rede virtual para **myVirtualNetwork**, e o nome de subnet para **mySubNet**. Substitua os nomes no exemplo pelos nomes dos seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Primeiro, validar que pode migrar a rede virtual utilizando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando seguinte apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode prosseguir com o seguinte passo preparar:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação Prepare ter sucesso com qualquer uma das opções anteriores, questione o estado de migração dos VMs. Certifique-se de que estão no estado `Prepared`.

Este exemplo define o nome VM para **myVM**. Substitua o nome de exemplo pelo seu próprio nome VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração dos recursos preparados utilizando o PowerShell ou o portal Azure. Se não está pronto para a migração e quer voltar ao antigo estado, use o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passo 5.1: Opção 2 - Migrar máquinas virtuais numa rede virtual

Para migrar máquinas virtuais numa rede virtual, migra-se a rede virtual. As máquinas virtuais migram automaticamente com a rede virtual. Escolha a rede virtual que quer migrar.
> [!NOTE]
> [Migrar uma única máquina virtual](migrate-single-classic-to-resource-manager.md) criada utilizando o modelo de implementação clássico, criando uma nova máquina virtual do Gestor de Recursos com Discos Geridos utilizando os ficheiros VHD (OS e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo portal. O novo portal Azure apresenta o nome como `[vnet-name]`, mas o nome real da rede virtual é de tipo `Group [resource-group-name] [vnet-name]`. Antes de iniciar a migração, procure o nome da rede virtual real usando o comando `Get-AzureVnetSite | Select -Property Name` ou veja-o no antigo portal Azure. 

Este exemplo define o nome da rede virtual para **myVnet**. Substitua o nome da rede virtual de exemplo pelo seu.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções web ou trabalhadores, ou VMs com configurações não suportadas, obtém uma mensagem de erro de validação.

Primeiro, validar que pode migrar a rede virtual utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando seguinte apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode prosseguir com o seguinte passo preparar:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração das máquinas virtuais preparadas utilizando o Azure PowerShell ou o portal Azure. Se não está pronto para a migração e quer voltar ao antigo estado, use o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Passo 5.2: Migrar uma conta de armazenamento
Depois de terminar de migrar as máquinas virtuais, efetue as seguintes verificações prévias antes de migrar as contas de armazenamento.

> [!NOTE]
> Se a sua conta de armazenamento não tiver discos associados ou dados VM, pode saltar diretamente para a secção "Validar contas de armazenamento e iniciar a migração".

* Verificações prévias se emigrou algum VMs ou a sua conta de armazenamento tem recursos de disco:
    * Migrar máquinas virtuais cujos discos estão armazenados na conta de armazenamento.

        O comando seguinte devolve as propriedades RoleName e DiskName de todos os discos VM na conta de armazenamento. Nome de papel é o nome da máquina virtual à qual um disco está ligado. Se este comando devolver os discos, certifique-se de que as máquinas virtuais às quais estes discos estão ligados são migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Eliminar discos VM não ligados armazenados na conta de armazenamento.

        Encontre discos VM não ligados na conta de armazenamento utilizando o seguinte comando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se o comando anterior devolver os discos, elimine estes discos utilizando o seguinte comando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Elimine as imagens VM armazenadas na conta de armazenamento.

        O comando seguinte devolve todas as imagens VM com discos OS armazenados na conta de armazenamento.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         O comando seguinte devolve todas as imagens VM com discos de dados armazenados na conta de armazenamento.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Elimine todas as imagens VM devolvidas pelos comandos anteriores utilizando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Valide as contas de armazenamento e inicie a migração.

    Valide cada conta de armazenamento para migração utilizando o seguinte comando. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    O próximo passo é preparar a conta de armazenamento para a migração.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verifique a configuração da conta de armazenamento preparada utilizando o Azure PowerShell ou o portal Azure. Se não está pronto para a migração e quer voltar ao antigo estado, use o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Passos seguintes
* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o CLI para migrar recursos IaaS do clássico para o Gestor de Recursos Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
