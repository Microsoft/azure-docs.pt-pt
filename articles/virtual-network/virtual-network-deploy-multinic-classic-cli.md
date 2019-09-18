---
title: Criar uma VM (clássica) com várias NICs – CLI clássica do Azure | Microsoft Docs
description: Saiba como criar uma VM (clássica) com várias NICs usando a CLI (interface de linha de comando) clássica do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058751"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Criar uma VM (clássica) com várias NICs usando a CLI clássica do Azure

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Você pode criar VMs (máquinas virtuais) no Azure e anexar várias NICs (interfaces de rede) a cada uma de suas VMs. Várias NICs habilitam a separação de tipos de tráfego entre NICs. Por exemplo, uma NIC pode se comunicar com a Internet, enquanto outra se comunica somente com recursos internos não conectados à Internet. A capacidade de separar o tráfego de rede entre várias NICs é necessária para muitos dispositivos de virtualização de rede, como soluções de entrega de aplicativos e de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como executar essas etapas usando o [modelo de implantação do Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para os servidores Web e um grupo de recursos chamado *IaaSStory-backend* para os servidores de banco de BD.

## <a name="prerequisites"></a>Pré-requisitos
Antes de criar os servidores de BD, você precisa criar o grupo de recursos *IaaSStory* com todos os recursos necessários para esse cenário. Para criar esses recursos, conclua as etapas a seguir. Crie uma rede virtual seguindo as etapas no artigo [criar uma rede virtual](virtual-networks-create-vnet-classic-cli.md) .

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implantar as VMs de back-end
As VMs de back-end dependem da criação dos seguintes recursos:

* **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados nos servidores de banco de dados usarão a tecnologia SSD (unidade de estado sólido), que requer uma conta de armazenamento Premium. Verifique se o local do Azure que você implanta para oferecer suporte ao armazenamento Premium.
* **NICs**. Cada VM terá duas NICs, uma para acesso ao banco de dados e outra para gerenciamento.
* **Conjunto de disponibilidade**. Todos os servidores de banco de dados serão adicionados a um único conjunto de disponibilidade para garantir que pelo menos uma das VMs esteja em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Etapa 1 – iniciar o script
Você pode baixar o script de bash completo usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Conclua as seguintes etapas para alterar o script para trabalhar em seu ambiente:

1. Altere os valores das variáveis abaixo com base em seu grupo de recursos existente implantado acima em [pré-requisitos](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Altere os valores das variáveis abaixo com base nos valores que você deseja usar para a implantação de back-end.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Etapa 2 – criar os recursos necessários para suas VMs
1. Crie um novo serviço de nuvem para todas as VMs de back-end. Observe o uso da `$backendCSName` variável para o nome do grupo de recursos e `$location` para a região do Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Crie uma conta de armazenamento Premium para o sistema operacional e os discos de dados a serem usados pelas suas VMs.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Etapa 3-criar VMs com várias NICs
1. Inicie um loop para criar várias VMS, com base nas `numberOfVMs` variáveis.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Para cada VM, especifique o nome e o endereço IP de cada uma das duas NICs.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Crie a VM. Observe o uso do `--nic-config` parâmetro, que contém uma lista de todas as NICs com nome, sub-rede e endereço IP.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Para cada VM, crie dois discos de dados.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Etapa 4 – executar o script
Agora que você baixou e alterou o script com base em suas necessidades, execute o script para criar VMs de banco de dados back-end com várias NICs.

1. Salve o script e execute-o do seu terminal **bash** . Você verá a saída inicial, conforme mostrado abaixo.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Após alguns minutos, a execução será encerrada e você verá o restante da saída, conforme mostrado abaixo.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Etapa 5 – configurar o roteamento dentro do sistema operacional da VM

O DHCP do Azure atribui um gateway padrão para a primeira interface de rede (primária) anexada à máquina virtual. O Azure não atribui um gateway predefinido a interfaces de rede (secundárias) adicionais ligadas a uma máquina virtual. Por conseguinte, não pode comunicar com recursos que estejam fora da sub-rede em que se encontre uma interface de rede secundária, por predefinição. Contudo, as interfaces de rede secundárias podem comunicar com recursos fora das respetivas sub-redes. Para configurar o roteamento para interfaces de rede secundárias, consulte [Roteamento em um sistema operacional de máquina virtual com várias interfaces de rede](virtual-network-network-interface-vm.md).
