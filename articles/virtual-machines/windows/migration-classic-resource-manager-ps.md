---
title: Migrar para o Gerenciador de recursos com o PowerShell
description: Este artigo percorre a migração de recursos de IaaS com suporte da plataforma, como VMs (máquinas virtuais), redes virtuais e contas de armazenamento do clássico para Azure Resource Manager usando comandos Azure PowerShell
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
ms.openlocfilehash: 0c145c84f9dea3b6212bb6c608eb1a4eb586ff60
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056823"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrar recursos de IaaS do clássico para o Azure Resource Manager usando o PowerShell
Estas etapas mostram como usar Azure PowerShell comandos para migrar recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o modelo de implantação de Azure Resource Manager.

Se quiser, também pode migrar recursos utilizando o [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração apoiados pela Plataforma, consulte [a migração apoiada pela Plataforma de recursos IaaS do clássico para](migration-classic-resource-manager-overview.md)o Gestor de Recursos Azure.
* Para obter orientações detalhadas e um passeio migratório, consulte [o mergulho técnico profundo na migração apoiada pela plataforma do clássico para o Gestor de Recursos Azure.](migration-classic-resource-manager-deep-dive.md)
* [Reveja os erros de migração mais comuns.](migration-classic-resource-manager-errors.md)

<br>
Aqui está um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração.

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Etapa 1: planejar a migração
Aqui estão algumas práticas recomendadas que recomendamos à medida que você avaliar se deseja migrar os recursos de IaaS do clássico para o Resource Manager:

* Leia através das [funcionalidades e configurações suportadas e não suportadas](migration-classic-resource-manager-overview.md). Se você tiver máquinas virtuais que usam recursos ou configurações sem suporte, aguarde até que a configuração ou o suporte a recursos sejam anunciados. Como alternativa, se atender às suas necessidades, remova esse recurso ou retire dessa configuração para habilitar a migração.
* Se você tiver scripts automatizados que implantam sua infraestrutura e seus aplicativos hoje, tente criar uma configuração de teste semelhante usando esses scripts para migração. Como alternativa, você pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente, os gateways de aplicativo não têm suporte para migração do clássico para o Gerenciador de recursos. Para migrar uma rede virtual com um gateway de aplicativo, remova o gateway antes de executar uma operação de preparação para mover a rede. Depois de concluir a migração, reconecte o gateway em Azure Resource Manager.
>
> Gateways do Azure ExpressRoute que se conectam a circuitos do ExpressRoute em outra assinatura não podem ser migrados automaticamente. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway. Para mais informações, consulte [os circuitos Migrate ExpressRoute e redes virtuais associadas do clássico ao modelo](../../expressroute/expressroute-migration-classic-resource-manager.md)de implementação do Gestor de Recursos.

## <a name="step-2-install-the-latest-version-of-powershell"></a>Etapa 2: instalar a versão mais recente do PowerShell
Existem duas opções principais para instalar o Azure PowerShell: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). O WebPI recebe atualizações mensais. Galeria do PowerShell recebe atualizações continuamente. Este artigo se baseia em Azure PowerShell versão 2.1.0.

Para instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Etapa 3: Verifique se você é um administrador da assinatura
Para realizar esta migração, deve ser adicionado como coadministrador para a subscrição no [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **Hub,** **selecione Subscrição**. Se não o vir, selecione **Todos os serviços.**
3. Encontre a entrada de subscrição apropriada e, em seguida, olhe para o campo **MY ROLE.** Para um coadministrador, o valor deve ser _administração da Conta_.

Se não for possível adicionar um coadministrador, entre em contato com um administrador de serviços ou coadministrador da assinatura para se tornar adicionado.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Etapa 4: definir sua assinatura e inscrever-se para a migração
Primeiro, inicie um prompt do PowerShell. Para a migração, configure seu ambiente para o clássico e o Resource Manager.

Entre em sua conta para o modelo do Resource Manager.

```powershell
    Connect-AzAccount
```

Obtenha as assinaturas disponíveis usando o seguinte comando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define o nome de subscrição predefinido para **a Subscrição My Azure**. Substitua o nome da assinatura de exemplo pelo seu próprio.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registro é uma etapa única, mas você deve fazê-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro:
>
> *BadRequest : A subscrição não está registada para migração.*

Registre-se com o provedor de recursos de migração usando o seguinte comando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para que o registro seja concluído. Verifique o status da aprovação usando o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Certifique-se de que o Estado de Registo está `Registered` antes de prosseguir.

Agora, entre em sua conta para o modelo de implantação clássico.

```powershell
    Add-AzureAccount
```

Obtenha as assinaturas disponíveis usando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define a subscrição padrão da **Assinatura My Azure.** Substitua o nome da assinatura de exemplo pelo seu próprio.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-have-enough-resource-manager-vm-vcpus"></a>Etapa 5: ter vCPUs de VM do Resource Manager suficientes
Verifique se você tem Azure Resource Manager de máquina virtual suficiente na região do Azure de sua implantação atual ou rede virtual. Você pode usar o seguinte comando do PowerShell para verificar o número atual de vCPUs que você tem em Azure Resource Manager. Para saber mais sobre as quotas vCPU, consulte [Limits e o Gestor de Recursos Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md#limits-and-azure-resource-manager)

Este exemplo verifica a disponibilidade na região **dos EUA Ocidentais.** Substitua o nome da região de exemplo por seu próprio.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Etapa 6: executar comandos para migrar seus recursos de IaaS
* [Migram VMs em um serviço de nuvem (não em uma rede virtual)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migram VMs em uma rede virtual](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar uma conta de armazenamento](#step-62-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se você tiver um problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tenta a ação novamente.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Etapa 6,1: opção 1-migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)
Obtenha a lista de serviços de nuvem usando o comando a seguir. Em seguida, escolha o serviço de nuvem que você deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual ou se tiverem funções Web ou de trabalho, o comando retornará uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome da implantação para o serviço de nuvem. Neste exemplo, o nome de serviço é **My Service**. Substitua o nome do serviço de exemplo pelo seu próprio nome de serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções para escolher.

* **Opção 1: Migrar os VMs para uma rede virtual criada pela plataforma.**

    Primeiro, valide que você pode migrar o serviço de nuvem usando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando a seguir exibe os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá passar para a etapa de preparação.

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

    Primeiro, valide que você pode migrar a rede virtual usando o seguinte comando:

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

Depois que a operação de preparação for realizada com sucesso com uma das opções anteriores, consulte o estado de migração das VMs. Certifique-se de que estão no estado `Prepared`.

Este exemplo define o nome VM para **myVM**. Substitua o nome de exemplo pelo seu próprio nome de VM.

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
> [Migrar uma única máquina virtual](migrate-single-classic-to-resource-manager.md) criada utilizando o modelo de implementação clássico, criando uma nova máquina virtual do Gestor de Recursos com Discos Geridos utilizando os ficheiros VHD (OS e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo Portal. O novo portal Azure apresenta o nome como `[vnet-name]`, mas o nome real da rede virtual é de tipo `Group [resource-group-name] [vnet-name]`. Antes de iniciar a migração, procure o nome da rede virtual real usando o comando `Get-AzureVnetSite | Select -Property Name` ou veja-o no antigo portal Azure. 

Este exemplo define o nome da rede virtual para **myVnet**. Substitua o nome de rede virtual de exemplo pelo seu próprio.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções Web ou de trabalho, ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Primeiro, valide que você pode migrar a rede virtual usando o seguinte comando:

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

### <a name="step-62-migrate-a-storage-account"></a>Etapa 6,2: migrar uma conta de armazenamento
Depois de terminar de migrar as máquinas virtuais, execute as seguintes verificações de pré-requisitos antes de migrar as contas de armazenamento.

> [!NOTE]
> Se sua conta de armazenamento não tiver discos associados ou dados de VM, você poderá pular diretamente para a seção "validar contas de armazenamento e iniciar a migração".

* As verificações de pré-requisitos se você migrou qualquer VM ou sua conta de armazenamento tem recursos de disco:
    * Migre máquinas virtuais cujos discos estejam armazenados na conta de armazenamento.

        O comando a seguir retorna as propriedades RoleName e diskname de todos os discos de VM na conta de armazenamento. RoleName é o nome da máquina virtual à qual um disco está anexado. Se esse comando retornar discos, verifique se as máquinas virtuais para as quais esses discos estão anexados são migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Exclua os discos de VM desanexados armazenados na conta de armazenamento.

        Localize discos de VM desconectados na conta de armazenamento usando o seguinte comando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se o comando anterior retornar discos, exclua esses discos usando o seguinte comando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Exclua as imagens de VM armazenadas na conta de armazenamento.

        O comando a seguir retorna todas as imagens de VM com discos de sistema operacional armazenados na conta de armazenamento.
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
        Exclua todas as imagens de VM retornadas pelos comandos anteriores usando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Valide as contas de armazenamento e inicie a migração.

    Valide cada conta de armazenamento para migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome do exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A próxima etapa é preparar a conta de armazenamento para a migração.

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
* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o CLI para migrar recursos IaaS do clássico para o Gestor de Recursos Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
