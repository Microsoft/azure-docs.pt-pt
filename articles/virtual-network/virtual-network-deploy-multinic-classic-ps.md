---
title: Criar uma VM (clássico) com vários NICs - Azure PowerShell | Documentos da Microsoft
description: Saiba como criar uma VM (clássico) com vários NICs com o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652200"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Criar uma VM (clássico) com vários NICs com o PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma das suas VMs. Várias NICs ative a separação de tipos de tráfego entre NICs. Por exemplo, uma NIC poderá comunicar com a Internet, enquanto outro comunica apenas com recursos internos não ligados à Internet. A capacidade de separar o tráfego de rede em vários NICs é necessária para muitas aplicações virtuais de rede, tais como entrega de aplicativos e soluções de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássica](../resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como executar estes passos com o [modelo de implementação do Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Os passos seguintes utilizam um grupo de recursos chamado *IaaSStory* para os servidores WEB e um grupo de recursos com o nome *IaaSStory-back-end* para os servidores DB.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar os servidores DB, tem de criar o *IaaSStory* grupo de recursos com todos os recursos necessários para este cenário. Para criar esses recursos, conclua os passos que se seguem. Criar uma rede virtual ao seguir os passos a [criar uma rede virtual](virtual-networks-create-vnet-classic-netcfg-ps.md) artigo.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Criar as VMs de back-end
As VMs de back-end dependem a criação dos seguintes recursos:

* **A sub-rede de back-end**. Os servidores de base de dados irão fazer parte de uma sub-rede separada, segregar o tráfego. Esta sub-rede existir numa vnet com o nome de espera que o script abaixo *WTestVnet*.
* **Conta de armazenamento para discos de dados**. Para um melhor desempenho, os discos de dados nos servidores de base de dados irão utilizar a tecnologia de unidade (SSD) de estado sólido, que requer uma conta de armazenamento premium. Certifique-se a localização do Azure que implementa para suportar o armazenamento premium.
* **Conjunto de disponibilidade**. Todos os servidores de base de dados serão adicionados a uma única conjunto de disponibilidade, para garantir, pelo menos, uma das VMs está ativo e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Passo 1: iniciar o seu script
Pode transferir o script do PowerShell completo utilizado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga os passos abaixo para alterar o script funcione no seu ambiente.

1. Alterar os valores das variáveis abaixo com base no seu grupo de recursos existentes implementado acima na [pré-requisitos](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Altere os valores das variáveis abaixo com base nos valores que pretende utilizar para a sua implementação de back-end.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passo 2 – criar recursos necessários para as suas VMs
Tem de criar um novo serviço cloud e uma conta de armazenamento para os discos de dados para todas as VMs. Também tem de especificar uma imagem e uma conta de administrador local para as VMs. Para criar esses recursos, conclua os seguintes passos:

1. Crie um novo serviço cloud.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Crie uma nova conta de armazenamento premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Defina a conta de armazenamento criada acima, como a conta de armazenamento atual para a sua subscrição.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Selecione uma imagem da VM.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Defina as credenciais da conta de administrador local.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Passo 3 - criar VMs
Precisa usar um loop para criar VMs quantos quiser, e criar as VMs e NICs necessários dentro do loop. Para criar as VMs e NICs, execute os seguintes passos.

1. Iniciar uma `for` loop repetir os comandos para criar uma VM e dois NICs, muitas vezes, se necessário, com base no valor da `$numberOfVMs` variável.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Criar um `VMConfig` objeto Especifica a imagem, o tamanho e o conjunto de disponibilidade para a VM.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Aprovisione a VM como um VM do Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Defina a predefinida NIC e atribua-lhe um endereço IP estático.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Adicione uma segunda NIC para cada VM.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Crie para discos de dados para cada VM.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Criar cada VM e encerrar o loop.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Passo 4 - executar o script
Agora que transferiu e alterou o script consoante as suas necessidades, runt o script para criar a base de dados de back-end VMs com várias NICs.

1. Guarde o seu script e executá-lo do **PowerShell** linha de comandos, ou **ISE do PowerShell**. Verá a saída inicial, conforme mostrado abaixo.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Preencha as informações necessárias no prompt de credenciais e clique **OK**. O seguinte resultado é devolvido.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Passo 5 - configurar o encaminhamento no sistema de operativo da VM

DHCP do Azure atribui um gateway predefinido para a primeira interface de rede (principal) ligado à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. Contudo, as interfaces de rede secundárias podem comunicar com recursos fora das respetivas sub-redes. Configurar o encaminhamento para interfaces de rede secundárias, veja os artigos seguintes:

- [Configurar uma VM do Windows para vários NICs](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Configurar uma VM do Linux para vários NICs](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
