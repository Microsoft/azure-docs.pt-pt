---
title: Criar uma VM (clássica) com várias NICs-Azure PowerShell | Microsoft Docs
description: Saiba como criar uma VM (clássica) com várias NICs usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058735"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Criar uma VM (clássica) com várias NICs usando o PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Você pode criar VMs (máquinas virtuais) no Azure e anexar várias NICs (interfaces de rede) a cada uma de suas VMs. Várias NICs habilitam a separação de tipos de tráfego entre NICs. Por exemplo, uma NIC pode se comunicar com a Internet, enquanto outra se comunica somente com recursos internos não conectados à Internet. A capacidade de separar o tráfego de rede entre várias NICs é necessária para muitos dispositivos de virtualização de rede, como soluções de entrega de aplicativos e de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como executar essas etapas usando o [modelo de implantação do Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para os servidores Web e um grupo de recursos chamado *IaaSStory-backend* para os servidores de banco de BD.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar os servidores de BD, você precisa criar o grupo de recursos *IaaSStory* com todos os recursos necessários para esse cenário. Para criar esses recursos, conclua as etapas a seguir. Crie uma rede virtual seguindo as etapas no artigo [criar uma rede virtual](virtual-networks-create-vnet-classic-netcfg-ps.md) .

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Criar as VMs de back-end
As VMs de back-end dependem da criação dos seguintes recursos:

* **Sub-rede de back-end**. Os servidores de banco de dados serão parte de uma sub-rede separada, para separar o tráfego. O script a seguir espera que essa sub-rede exista em uma vnet chamada *WTestVnet*.
* **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados nos servidores de banco de dados usarão a tecnologia SSD (unidade de estado sólido), que requer uma conta de armazenamento Premium. Verifique se o local do Azure que você implanta para oferecer suporte ao armazenamento Premium.
* **Conjunto de disponibilidade**. Todos os servidores de banco de dados serão adicionados a um único conjunto de disponibilidade para garantir que pelo menos uma das VMs esteja em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Etapa 1 – iniciar o script
Você pode baixar o script completo do PowerShell usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga as etapas abaixo para alterar o script para trabalhar em seu ambiente.

1. Altere os valores das variáveis abaixo com base em seu grupo de recursos existente implantado acima em [pré-requisitos](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Altere os valores das variáveis abaixo com base nos valores que você deseja usar para a implantação de back-end.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Etapa 2 – criar os recursos necessários para suas VMs
Você precisa criar um novo serviço de nuvem e uma conta de armazenamento para os discos de dados para todas as VMs. Você também precisa especificar uma imagem e uma conta de administrador local para as VMs. Para criar esses recursos, conclua as seguintes etapas:

1. Crie um novo serviço de nuvem.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Crie uma nova conta de armazenamento Premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Defina a conta de armazenamento criada acima como a conta de armazenamento atual para sua assinatura.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Selecione uma imagem para a VM.

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

### <a name="step-3---create-vms"></a>Etapa 3 – criar VMs
Você precisa usar um loop para criar quantas VMs desejar e criar as NICs e as VMs necessárias dentro do loop. Para criar NICs e VMs, execute as etapas a seguir.

1. Inicie um `for` loop para repetir os comandos para criar uma VM e duas NICs quantas vezes forem necessárias, com base no valor `$numberOfVMs` da variável.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Crie um `VMConfig` objeto especificando a imagem, o tamanho e o conjunto de disponibilidade para a VM.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Provisione a VM como uma VM do Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Defina a NIC padrão e atribua a ela um endereço IP estático.

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
    
6. Crie em discos de dados para cada VM.

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

7. Crie cada VM e encerre o loop.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Etapa 4 – executar o script
Agora que você baixou e alterou o script com base em suas necessidades, o Runt é o script para criar VMs de banco de dados back-end com várias NICs.

1. Salve o script e execute-o no prompt de comando do **PowerShell** ou no **ISE do PowerShell**. Você verá a saída inicial, conforme mostrado abaixo.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Preencha as informações necessárias no prompt de credenciais e clique em **OK**. A saída a seguir é retornada.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Etapa 5 – configurar o roteamento dentro do sistema operacional da VM

O DHCP do Azure atribui um gateway padrão para a primeira interface de rede (primária) anexada à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. Contudo, as interfaces de rede secundárias podem comunicar com recursos fora das respetivas sub-redes. Para configurar o roteamento para interfaces de rede secundárias, consulte os seguintes artigos:

- [Configurar uma VM do Windows para várias NICs](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Configurar uma VM do Linux para várias NICs](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
