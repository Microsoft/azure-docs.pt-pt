---
title: Migrar para o Resource Manager com o PowerShell | Documentos da Microsoft
description: Este artigo explica a migração suportada por plataforma de recursos de IaaS, como máquinas virtuais (VMs), redes virtuais (VNETs) e contas de armazenamento do clássico para o Azure Resource Manager (ARM) ao utilizar comandos do Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 02b3f4b0db46d05751569d465abf09310be91e8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549780"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar recursos de IaaS do clássico para o Azure Resource Manager com o Azure PowerShell
Estes passos mostram como utilizar comandos do Azure PowerShell para migrar a infraestrutura como um recursos de serviço (IaaS) do modelo de implementação clássica para o modelo de implementação Azure Resource Manager.

Se quiser, também pode migrar recursos utilizando o [Interface de linha de comandos do Azure (CLI do Azure)](../linux/migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração suportados, veja [migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Para obter instruções detalhadas e uma migração passo a passo, consulte [técnica aprofundada da migração suportada por plataforma de clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)

<br>
Eis um fluxograma para identificar a ordem em que passos têm de ser executado durante um processo de migração

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-plan-for-migration"></a>Passo 1: Planear a migração
Seguem-se algumas melhores práticas que recomendamos durante a avaliação migrar recursos de IaaS do clássico para Resource Manager:

* Leia os [suportados e funcionalidades não suportadas e configurações](migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizem as configurações não suportadas ou funcionalidades, recomendamos que aguarde o suporte de configuração/funcionalidades serão anunciados. Em alternativa, se ele atenda às suas necessidades, remover essa funcionalidade ou mover para fora de que a configuração para ativar a migração.
* Se tem automatizadas scripts que implemente a sua infraestrutura e aplicações hoje mesmo, tente criar uma configuração de teste semelhante com esses scripts para a migração. Em alternativa, pode configurar ambientes de exemplo com o portal do Azure.

> [!IMPORTANT]
> Gateways de aplicação não são atualmente suportados para migração da implementação clássica para Resource Manager. Para migrar uma rede virtual clássica com um gateway de aplicação, remova o gateway antes de executar uma operação de preparação para mover a rede. Depois de concluir a migração, volte a ligar o gateway no Azure Resource Manager.
>
>Ligar a circuitos do ExpressRoute noutra subscrição gateways do ExpressRoute não podem ser migradas automaticamente. Nesses casos, remover o gateway do ExpressRoute, migrar a rede virtual e recriar o gateway. Veja [Migrate ExpressRoute circuits e associadas a redes virtuais do clássico para o modelo de implementação do Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Passo 2: Instalar a versão mais recente do Azure PowerShell
Existem duas opções principais para instalar o Azure PowerShell: [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web (WebPI) de instalador de plataforma](https://aka.ms/webpi-azps). WebPI recebe atualizações mensais. Galeria do PowerShell recebe atualizações de forma contínua. Este artigo baseia-se a versão 2.1.0 do Azure PowerShell.

Para obter instruções de instalação, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Passo 3: Certifique-se de que é um administrador para a subscrição no portal do Azure
Para efetuar a migração, tem de ser adicionado como um coadministrador da subscrição no [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **subscrição**. Se não o vir, selecione **todos os serviços**.
3. Localizar a entrada de subscrição adequada, em seguida, examinar os **minha função** campo. Para um coadministrador, o valor deve estar _administrador de conta_.

Se não for possível adicionar um coadministrador, em seguida, contacte um administrador de serviços ou coadministrador da subscrição para ficar-adicionado.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passo 4: Definir a subscrição e inscreva-se para a migração
Primeiro, inicie uma linha de comandos do PowerShell. Para a migração, terá de configurar o ambiente para clássicas e do Resource Manager.

Inicie sessão na sua conta para o modelo do Resource Manager.

```powershell
    Connect-AzAccount
```

Com o seguinte comando para obter as subscrições disponíveis:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Defina a sua subscrição do Azure para a sessão atual. Este exemplo define o nome da subscrição predefinida como **a minha subscrição do Azure**. Substitua o nome da subscrição de exemplo pelos seus próprios.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registo é uma etapa única, mas deve fazê-lo uma vez antes de tentar migrar. Sem se registrar, verá a seguinte mensagem de erro:
>
> *BadRequest: Subscrição não está registada para migração.*

Registe-se com o fornecedor de recursos de migração utilizando o seguinte comando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos a concluir do registo. Pode verificar o estado da aprovação com o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Certifique-se de que é RegistrationState `Registered` antes de continuar.

Agora iniciar sessão sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Com o seguinte comando para obter as subscrições disponíveis:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina a sua subscrição do Azure para a sessão atual. Este exemplo define a assinatura padrão, como **a minha subscrição do Azure**. Substitua o nome da subscrição de exemplo pelos seus próprios.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passo 5: Certifique-se de que tem suficiente vCPUs de Máquina Virtual do Azure Resource Manager na região do Azure da sua implementação atual ou a VNET
Pode utilizar o seguinte comando do PowerShell para verificar o número atual de vCPUs que tiver no Azure Resource Manager. Para saber mais sobre as quotas de vCPU, veja [limites e o Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Este exemplo verifica a disponibilidade **E.U.A. oeste** região. Substitua o nome da região de exemplo pelos seus próprios.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Passo 6: Execute comandos para migrar os recursos de IaaS
* [Migrar VMs num serviço cloud (não na rede virtual)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrar VMs numa rede virtual](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar a conta de armazenamento](#step-62-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotent. Se tiver um problema que não seja um recurso não suportado ou um erro de configuração, recomendamos que repita a preparação, aborto ou consolidação a operação. A plataforma tenta, em seguida, a ação.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passo 6.1: Opção 1 - migrar máquinas virtuais num serviço cloud (não numa rede virtual)
Obter a lista de serviços cloud, utilizando o comando seguinte e, em seguida, selecione o serviço de nuvem que pretende migrar. Se as VMs no serviço cloud estiverem numa rede virtual ou se tiverem funções web ou de trabalho, o comando devolve uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome de implementação para o serviço cloud. Neste exemplo, o nome do serviço é **meu serviço**. Substitua o nome do serviço de exemplo com o nome do serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço cloud para a migração. Tem duas opções à sua escolha.

* **Opção 1. Migrar as VMs a uma rede virtual criada por plataforma**

    Em primeiro lugar, valide se é possível migrar o serviço em nuvem com os comandos seguintes:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando seguinte apresenta todos os avisos e erros que bloquear a migração. Se a validação é efetuada com êxito, então é possível mover para o **preparar** passo:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2. Migrar para uma rede virtual existente no modelo de implementação do Resource Manager**

    Este exemplo define o nome do grupo de recursos, como **myResourceGroup**, o nome de rede virtual para **myVirtualNetwork** e o nome da sub-rede para **mySubNet**. Substitua os nomes no exemplo com os nomes dos seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Em primeiro lugar, valide se é possível migrar a rede virtual com o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando seguinte apresenta todos os avisos e erros que bloquear a migração. Se a validação for bem-sucedida, em seguida, pode continuar com o passo de preparação seguinte:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação de preparação ser bem sucedida com qualquer uma das opções anteriores, consulte o estado de migração das VMs. Certifique-se de que eles estão o `Prepared` estado.

Este exemplo define o nome da VM como **myVM**. Substitua o nome de exemplo com o seu próprio nome VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração para os recursos preparados, utilizando o PowerShell ou o portal do Azure. Se não está preparado para migração e pretender voltar atrás para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada parece bom, pode seguir em frente e consolide os recursos com o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passo 6.1: Opção 2 - migrar máquinas virtuais numa rede virtual

Para migrar máquinas virtuais numa rede virtual, migre a rede virtual. As máquinas virtuais serão migrados automaticamente com a rede virtual. Escolha a rede virtual que pretende migrar.
> [!NOTE]
> [Migrar a máquina virtual clássica única](migrate-single-classic-to-resource-manager.md) através da criação de uma nova máquina virtual de Gestor de recursos com os Managed Disks, usando os arquivos VHD (SO e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome de rede virtual pode ser diferente das apresentadas no novo Portal. O novo Portal do Azure apresenta o nome como `[vnet-name]` mas o nome de rede virtual real é do tipo `Group [resource-group-name] [vnet-name]`. Antes de migrar, pesquisa o nome de rede virtual real com o comando `Get-AzureVnetSite | Select -Property Name` nem vê-los no Portal do Azure antiga. 

Este exemplo define o nome de rede virtual, como **myVnet**. Substitua o nome de rede virtual de exemplo com os seus próprios.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contém VMs ou funções de trabalho ou web com as configurações não suportadas, receberá uma mensagem de erro de validação.

Em primeiro lugar, valide se é possível migrar a rede virtual com o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando seguinte apresenta todos os avisos e erros que bloquear a migração. Se a validação for bem-sucedida, em seguida, pode continuar com o passo de preparação seguinte:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas com o Azure PowerShell ou o portal do Azure. Se não está preparado para migração e pretender voltar atrás para o estado antigo, utilize o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada parece bom, pode seguir em frente e consolide os recursos com o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>A etapa 6.2 para migrar uma conta de armazenamento
Quando tiver terminado a migrar as máquinas virtuais, recomendamos que executar as verificações de pré-requisitos seguintes antes de migrar as contas de armazenamento.

> [!NOTE]
> Se a sua conta de armazenamento tivesse sem discos associados ou dados da VM, pode avançar diretamente para o **validar a conta de armazenamento e começar a migração** secção.

* **Verificações de pré-requisitos, se tiver migrado qualquer VMs ou a conta de armazenamento tem recursos de disco**
    * **Migrar máquinas virtuais clássicas, cujos discos são armazenados na conta de armazenamento**

        O comando seguinte devolve RoleName e DiskName propriedades de todos os discos VM clássicas na conta de armazenamento. RoleName é o nome da máquina virtual à qual está ligado um disco. Se este comando devolve discos, em seguida, certifique-se de que as máquinas virtuais para que estes discos são ligados são migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Eliminar discos VM clássicos desanexados armazenados na conta de armazenamento**

        Encontre discos VM clássicos desanexados no armazenamento de conta através do seguinte comando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se acima do comando devolve discos, em seguida, elimine estes discos com os seguintes comandos:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Imagens de VM DELETE armazenadas na conta de armazenamento**

        O comando seguinte devolve todas as imagens VM com disco de SO armazenado na conta de armazenamento.
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
        Elimine todas as imagens VM devolvidas pelo acima de comandos utilizando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Validar o armazenamento de conta e iniciar a migração**

    Valide cada conta de armazenamento para a migração usando o seguinte comando. Neste exemplo, é o nome da conta de armazenamento **myStorageAccount**. Substitua o nome de exemplo com o nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Passo seguinte consiste em preparar a conta de armazenamento para migração

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verificar a configuração para a conta de armazenamento preparado com o Azure PowerShell ou o portal do Azure. Se não está preparado para migração e pretender voltar atrás para o estado antigo, utilize o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada parece bom, pode seguir em frente e consolide os recursos com o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral da migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da Comunidade para auxiliar na migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
