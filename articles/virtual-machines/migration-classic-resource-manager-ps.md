---
title: Migrar para Gestor de Recursos com PowerShell
description: Este artigo percorre a migração suportada pela plataforma de recursos IaaS, como máquinas virtuais (VMs), redes virtuais e contas de armazenamento de clássicos para Azure Resource Manager utilizando comandos Azure PowerShell
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 82eaa597796bf3772faa90a9dbc4151da935c46a
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027728"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrar recursos IaaS do clássico para o Azure Resource Manager usando o PowerShell

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](classic-vm-deprecation.md#how-does-this-affect-me)

Estes passos mostram-lhe como usar comandos Azure PowerShell para migrar a infraestrutura como um serviço (IaaS) recursos do modelo de implementação clássico para o modelo de implementação do Azure Resource Manager.

Se quiser, também pode migrar recursos utilizando o [CLI Azure](migration-classic-resource-manager-cli.md).

* Para obter antecedentes em cenários de migração apoiados, consulte [a migração de recursos iaaS suportados pela plataforma do clássico para o Azure Resource Manager.](migration-classic-resource-manager-overview.md)
* Para obter orientações detalhadas e uma passagem de migração, consulte [o mergulho técnico profundo na migração apoiada pela plataforma do clássico para o Gestor de Recursos Azure.](migration-classic-resource-manager-deep-dive.md)
* [Reveja os erros de migração mais comuns.](migration-classic-resource-manager-errors.md)

<br>
Aqui está um fluxograma para identificar a ordem em que os passos precisam ser executados durante um processo de migração.

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Passo 1: Plano de migração
Aqui estão algumas boas práticas que recomendamos à medida que avalia se migra os recursos iaaS do clássico para o Gestor de Recursos:

* Leia as [funcionalidades e configurações suportadas e não apoiadas](migration-classic-resource-manager-overview.md). Se tiver máquinas virtuais que utilizem configurações ou funcionalidades não suportadas, aguarde que a configuração ou suporte de funcionalidades seja anunciado. Em alternativa, se se adequar às suas necessidades, remova essa funcionalidade ou saia dessa configuração para permitir a migração.
* Se tiver scripts automatizados que implementem a sua infraestrutura e aplicações hoje em dia, tente criar uma configuração de teste semelhante usando esses scripts para migração. Em alternativa, pode configurar ambientes de amostra utilizando o portal Azure.

> [!IMPORTANT]
> As portas de aplicação não são atualmente suportadas para a migração do clássico para o Gestor de Recursos. Para migrar uma rede virtual com um gateway de aplicação, remova o gateway antes de executar uma operação Prepare para mover a rede. Depois de concluir a migração, reconecte a porta de entrada no Azure Resource Manager.
>
> Os gateways Azure ExpressRoute que se ligam aos circuitos ExpressRoute noutra subscrição não podem ser migrados automaticamente. Nesses casos, remova o gateway ExpressRoute, migrar a rede virtual e recriar o gateway. Para obter mais informações, consulte [os circuitos Migrate ExpressRoute e as redes virtuais associadas do clássico ao modelo de implementação do Gestor de Recursos.](../expressroute/expressroute-migration-classic-resource-manager.md)

## <a name="step-2-install-the-latest-version-of-powershell"></a>Passo 2: Instalar a versão mais recente do PowerShell
Existem duas opções principais para instalar a Azure PowerShell: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A PowerShell Gallery recebe atualizações continuamente. Este artigo baseia-se na versão 2.1.0 da Azure PowerShell.

Para obter instruções de instalação, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Passo 3: Certifique-se de que é administrador da subscrição
Para realizar esta migração, tem de ser adicionado como coadministrator para a subscrição no [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **Hub,** **selecione Subscrição**. Se não o vir, selecione **Todos os serviços**.
3. Encontre a entrada de subscrição adequada e, em seguida, olhe para o campo **MY ROLE.** Para um coadministrador, o valor deve ser _a administração da conta._

Se não conseguir adicionar um coadministrador, contacte um administrador de serviço ou coadministrador para que a subscrição seja adicionada.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passo 4: Descreva a sua subscrição e inscreva-se para a migração
Primeiro, inicie uma solicitação powerShell. Para a migração, crie o seu ambiente tanto para o clássico como para o Gestor de Recursos.

Inscreva-se na sua conta para o modelo gestor de recursos.

```powershell
    Connect-AzAccount
```

Obtenha as subscrições disponíveis utilizando o seguinte comando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Desconfie da sua subscrição Azure para a sessão atual. Este exemplo define o nome de subscrição padrão da **Subscrição My Azure**. Substitua o nome de assinatura exemplo pelo seu próprio.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registo é um passo único, mas deve fazê-lo uma vez antes de tentar a migração. Sem se registar, vê a seguinte mensagem de erro:
>
> *BadRequest : A subscrição não está registada para migração.*

Registe-se junto do fornecedor de recursos migratório utilizando o seguinte comando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Espere cinco minutos para a inscrição terminar. Verifique o estado da aprovação utilizando o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Certifique-se de que o Estado de Registo está `Registered` antes de prosseguir.

Antes de mudar para o modelo de implementação clássico, certifique-se de que tem vCPUs de máquina virtual Azure Resource Manager suficientes na região Azure da sua atual implementação ou rede virtual. Pode utilizar o seguinte comando PowerShell para verificar o número atual de vCPUs que tem no Azure Resource Manager. Para saber mais sobre as quotas vCPU, consulte [Limits e o Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Este exemplo verifica a disponibilidade na região oeste **dos EUA.** Substitua o nome da região de exemplo pelo seu próprio.

```powershell
    Get-AzVMUsage -Location "West US"
```

Agora, inscreva-se na sua conta para o modelo clássico de implementação.

```powershell
    Add-AzureAccount
```

Obtenha as subscrições disponíveis utilizando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Desconfie da sua subscrição Azure para a sessão atual. Este exemplo define a subscrição padrão da **Subscrição My Azure**. Substitua o nome de assinatura exemplo pelo seu próprio.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Passo 5: Executar comandos para migrar os seus recursos IaaS
* [Migrar VMs num serviço de nuvem (não numa rede virtual)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrar VMs numa rede virtual](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar uma conta de armazenamento](#step-52-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se tiver outro problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que relemisse a preparação, abortar ou cometer o funcionamento. A plataforma tenta a ação novamente.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passo 5.1: Opção 1 - Migrar máquinas virtuais num serviço de nuvem (não numa rede virtual)
Obtenha a lista de serviços em nuvem utilizando o seguinte comando. Em seguida, escolha o serviço de nuvem que quer migrar. Se os VMs do serviço de nuvem estiverem numa rede virtual ou tiverem funções web ou de trabalhador, o comando devolve uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome de implantação para o serviço de nuvem. Neste exemplo, o nome de serviço é **My Service**. Substitua o nome de serviço de exemplo pelo seu próprio nome de serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Tem duas opções para escolher.

* **Opção 1: Migrar os VMs para uma rede virtual criada pela plataforma.**

    Em primeiro lugar, valide que pode migrar o serviço de nuvem utilizando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O seguinte comando apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode passar ao passo preparar.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2: Migrar para uma rede virtual existente no modelo de implementação do Gestor de Recursos.**

    Este exemplo define o nome do grupo de recursos para **myResourceGroup**, o nome de rede virtual para **myVirtualNetwork**, e o nome da sub-rede para **mySubNet**. Substitua os nomes no exemplo pelos nomes dos seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Em primeiro lugar, valide que pode migrar a rede virtual utilizando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O seguinte comando apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode proceder com o seguinte passo preparar:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação Preparação ter sucesso com qualquer uma das opções anteriores, consultar o estado de migração dos VMs. Certifique-se de que estão no `Prepared` estado.

Este exemplo define o nome VM ao **myVM**. Substitua o nome do exemplo pelo seu próprio nome VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração dos recursos preparados utilizando o PowerShell ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passo 5.1: Opção 2 - Migrar máquinas virtuais numa rede virtual

Para migrar máquinas virtuais numa rede virtual, migra a rede virtual. As máquinas virtuais migram automaticamente com a rede virtual. Escolha a rede virtual que pretende migrar.
> [!NOTE]
> [Migrar uma única máquina virtual](./windows/create-vm-specialized-portal.md) criada utilizando o modelo de implementação clássico, criando uma nova máquina virtual Do Gestor de Recursos com Discos Geridos utilizando os ficheiros VHD (OS e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo portal. O novo portal Azure apresenta o nome como `[vnet-name]` , mas o nome de rede virtual real é do tipo `Group [resource-group-name] [vnet-name]` . Antes de iniciar a migração, procure o nome da rede virtual, usando o comando `Get-AzureVnetSite | Select -Property Name` ou o veja no antigo portal Azure. 

Este exemplo define o nome da rede virtual para **myVnet**. Substitua o nome de rede virtual exemplo pelo seu próprio.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções web ou de trabalhador, ou VMs com configurações não suportadas, obtém-se uma mensagem de erro de validação.

Em primeiro lugar, valide que pode migrar a rede virtual utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O seguinte comando apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode proceder com o seguinte passo preparar:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração das máquinas virtuais preparadas utilizando o Azure PowerShell ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Passo 5.2: Migrar uma conta de armazenamento
Depois de ter acabado de migrar as máquinas virtuais, efetue as seguintes verificações pré-requisitos antes de migrar as contas de armazenamento.

> [!NOTE]
> Se a sua conta de armazenamento não tiver discos associados ou dados VM, pode saltar diretamente para a secção "Validar contas de armazenamento e iniciar a migração". Note também que a eliminação dos discos clássicos, imagens VM ou imagens de SO não remove os ficheiros VHD de origem na conta de armazenamento. No entanto, quebra o arrendamento desses ficheiros VHD para que possam ser reutilizados para criar discos ARM ou imagens após a migração.

* Os pré-requisitos verificam se emigrou quaisquer VMs ou se a sua conta de armazenamento tem recursos de disco:
    * Migrar máquinas virtuais cujos discos estão armazenados na conta de armazenamento.

        O comando seguinte devolve as propriedades RoleName e DiskName de todos os discos VM na conta de armazenamento. RoleName é o nome da máquina virtual à qual um disco está ligado. Se este comando devolver os discos, certifique-se de que as máquinas virtuais a que estes discos estão ligados são migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Elimine os discos VM não ligados armazenados na conta de armazenamento.

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

        O comando seguinte devolve todas as imagens VM com discos DE armazenados na conta de armazenamento.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         O seguinte comando devolve todas as imagens VM com discos de dados armazenados na conta de armazenamento.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Elimine todas as imagens VM devolvidas pelos comandos anteriores utilizando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Validar as contas de armazenamento e iniciar a migração.

    Validar cada conta de armazenamento para a migração utilizando o seguinte comando. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome do exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    O próximo passo é preparar a conta de armazenamento para a migração.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verifique a configuração da conta de armazenamento preparada utilizando o Azure PowerShell ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Passos seguintes
* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Ferramentas comunitárias para ajudar na migração de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
