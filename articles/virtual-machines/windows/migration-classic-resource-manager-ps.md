---
title: Migrar para o Gerenciador de recursos com o PowerShell | Microsoft Docs
description: Este artigo percorre a migração de recursos de IaaS com suporte da plataforma, como máquinas virtuais (VMs), redes virtuais (VNETs) e contas de armazenamento de clássico para Azure Resource Manager (ARM) usando comandos Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 01d5670add82291cb91264ab41fcd312a338840c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749336"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar recursos de IaaS do clássico para o Azure Resource Manager usando Azure PowerShell
Estas etapas mostram como usar Azure PowerShell comandos para migrar recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o modelo de implantação de Azure Resource Manager.

Se desejar, você também pode migrar recursos usando a [interface de linha de comando do Azure (CLI do Azure)](../linux/migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração com suporte, consulte [migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Para obter orientações detalhadas e instruções de migração, consulte aprofundamento [técnico sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)

<br>
Aqui está um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Etapa 1: planejar a migração
Aqui estão algumas práticas recomendadas que recomendamos ao avaliar a migração de recursos de IaaS do clássico para o Resource Manager:

* Leia os [recursos e as configurações com](migration-classic-resource-manager-overview.md)e sem suporte. Se você tiver máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos que aguarde até que o suporte de configuração/recurso seja anunciado. Como alternativa, se atender às suas necessidades, remova esse recurso ou retire dessa configuração para habilitar a migração.
* Se você tiver scripts automatizados que implantam sua infraestrutura e seus aplicativos hoje, tente criar uma configuração de teste semelhante usando esses scripts para migração. Como alternativa, você pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente, os gateways de aplicativo não têm suporte para migração do clássico para o Gerenciador de recursos. Para migrar uma rede virtual clássica com um gateway de aplicativo, remova o gateway antes de executar uma operação de preparação para mover a rede. Depois de concluir a migração, reconecte o gateway em Azure Resource Manager.
>
>Os gateways de ExpressRoute que se conectam a circuitos do ExpressRoute em outra assinatura não podem ser migrados automaticamente. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway. Consulte [migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Gerenciador de recursos](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Etapa 2: instalar a versão mais recente do Azure PowerShell
Há duas opções principais para instalar Azure PowerShell: [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). O WebPI recebe atualizações mensais. Galeria do PowerShell recebe atualizações continuamente. Este artigo se baseia em Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Etapa 3: Verifique se você é um administrador da assinatura no portal do Azure
Para executar essa migração, você deve ser adicionado como um coadministrador para a assinatura no [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **assinatura**. Se você não o vir, selecione **todos os serviços**.
3. Localize a entrada de assinatura apropriada e, em seguida, examine o campo **minha função** . Para um coadministrador, o valor deve ser _administrador da conta_.

Se você não conseguir adicionar um coadministrador, entre em contato com um administrador de serviços ou coadministrador da assinatura para se tornar adicionado.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Etapa 4: definir sua assinatura e inscrever-se para a migração
Primeiro, inicie um prompt do PowerShell. Para a migração, você precisa configurar seu ambiente para o clássico e o Resource Manager.

Entre em sua conta para o modelo do Resource Manager.

```powershell
    Connect-AzAccount
```

Obtenha as assinaturas disponíveis usando o seguinte comando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define o nome da assinatura padrão como **minha assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo seu próprio.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registro é uma etapa única, mas você deve fazê-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro:
>
> *BadRequest: a assinatura não está registrada para migração.*

Registre-se com o provedor de recursos de migração usando o seguinte comando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para que o registro seja concluído. Você pode verificar o status da aprovação usando o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique se o Registrostate está `Registered` antes de continuar.

Agora, entre em sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Obtenha as assinaturas disponíveis usando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define a assinatura padrão para **minha assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo seu próprio.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 5: Verifique se você tem o suficiente Azure Resource Manager máquina virtual vCPUs na região do Azure de sua implantação atual ou VNET
Você pode usar o seguinte comando do PowerShell para verificar o número atual de vCPUs que você tem em Azure Resource Manager. Para saber mais sobre as cotas do vCPU, consulte [limites e o Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Este exemplo verifica a disponibilidade na região **oeste dos EUA** . Substitua o nome da região de exemplo por seu próprio.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Etapa 6: executar comandos para migrar seus recursos de IaaS
* [Migrar VMs em um serviço de nuvem (não na rede virtual)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrar VMs em uma rede virtual](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar conta de armazenamento](#step-62-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se você tiver um problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tenta a ação novamente.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Etapa 6,1: opção 1-migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)
Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que você deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual ou se tiverem funções Web ou de trabalho, o comando retornará uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome da implantação para o serviço de nuvem. Neste exemplo, o nome do serviço é **meu serviço**. Substitua o nome do serviço de exemplo pelo seu próprio nome de serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções para escolher.

* **Opção 1. Migrar as VMs para uma rede virtual criada por plataforma**

    Primeiro, valide se você pode migrar o serviço de nuvem usando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando a seguir exibe os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá passar para a etapa de **preparação** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2. Migrar para uma rede virtual existente no modelo de implantação do Gerenciador de recursos**

    Este exemplo define o nome do grupo de recursos como **MyResource**Group, o nome da rede virtual como **myVirtualNetwork** e o nome da sub-rede como **mysubnet**. Substitua os nomes no exemplo pelos nomes dos seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando a seguir exibe os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de preparação:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Depois que a operação de preparação for realizada com sucesso com uma das opções anteriores, consulte o estado de migração das VMs. Verifique se eles estão no estado de `Prepared`.

Este exemplo define o nome da VM como **myVM**. Substitua o nome de exemplo pelo seu próprio nome de VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração dos recursos preparados usando o PowerShell ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada estiver correta, você poderá avançar e confirmar os recursos usando o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Etapa 6,1: opção 2-migrar máquinas virtuais em uma rede virtual

Para migrar máquinas virtuais em uma rede virtual, você migra a rede virtual. As máquinas virtuais são migradas automaticamente com a rede virtual. Escolha a rede virtual que você deseja migrar.
> [!NOTE]
> [Migre uma máquina virtual clássica única](migrate-single-classic-to-resource-manager.md) criando uma nova máquina virtual do Resource Manager com Managed disks usando os arquivos VHD (so e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo Portal. O novo portal do Azure exibe o nome como `[vnet-name]`, mas o nome real da rede virtual é do tipo `Group [resource-group-name] [vnet-name]`. Antes de migrar, pesquise o nome da rede virtual real usando o comando `Get-AzureVnetSite | Select -Property Name` ou exiba-o no portal do Azure antigo. 

Este exemplo define o nome da rede virtual como **myVnet**. Substitua o nome de rede virtual de exemplo pelo seu próprio.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções Web ou de trabalho, ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando a seguir exibe os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de preparação:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração das máquinas virtuais preparadas usando Azure PowerShell ou a portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada estiver correta, você poderá avançar e confirmar os recursos usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Etapa 6,2 migrar uma conta de armazenamento
Quando terminar de migrar as máquinas virtuais, recomendamos que você execute as seguintes verificações de pré-requisitos antes de migrar as contas de armazenamento.

> [!NOTE]
> Se sua conta de armazenamento não tiver discos associados ou dados de VM, você poderá pular diretamente para a seção **validar a conta de armazenamento e iniciar a migração** .

* **Verificações de pré-requisitos se você migrou alguma VM ou sua conta de armazenamento tem recursos de disco**
    * **Migrar máquinas virtuais clássicas cujos discos são armazenados na conta de armazenamento**

        O comando a seguir retorna as propriedades RoleName e diskname de todos os discos de VM clássicos na conta de armazenamento. RoleName é o nome da máquina virtual à qual um disco está anexado. Se esse comando retornar discos, verifique se as máquinas virtuais às quais esses discos estão anexados são migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Excluir discos de VM clássicas desanexados armazenados na conta de armazenamento**

        Localize os discos de VM clássicas desanexados na conta de armazenamento usando o seguinte comando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se o comando acima retornar discos, exclua esses discos usando o seguinte comando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Excluir imagens de VM armazenadas na conta de armazenamento**

        O comando a seguir retorna todas as imagens de VM com o disco do sistema operacional armazenado na conta de armazenamento.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         O comando a seguir retorna todas as imagens de VM com discos de dados armazenados na conta de armazenamento.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Exclua todas as imagens de VM retornadas pelos comandos acima usando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Validar a conta de armazenamento e iniciar a migração**

    Valide cada conta de armazenamento para migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome do exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A próxima etapa é preparar a conta de armazenamento para a migração

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verifique a configuração da conta de armazenamento preparada usando Azure PowerShell ou a portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada estiver correta, você poderá avançar e confirmar os recursos usando o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Passos seguintes
* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examine as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
