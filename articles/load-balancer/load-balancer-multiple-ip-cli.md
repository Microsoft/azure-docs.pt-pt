---
title: Equilíbrio de carga em várias configurações IP usando O ClI Azure
titleSuffix: Azure Load Balancer
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o Azure CLI.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 69d324647af014a5122c404929c104a9077d5f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225296"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Equilíbrio de carga em várias configurações IP usando O ClI Azure

Este artigo descreve como utilizar o Azure Load Balancer com vários endereços IP numa interface de rede secundária (NIC). Para este cenário, temos dois VMs a executar o Windows, cada um com um NIC primário e um NIC secundário. Cada um dos NICs secundários tem duas configurações IP. Cada VM acolhe os dois websites contoso.com e fabrikam.com. Cada website está ligado a uma das configurações IP no NIC secundário. Utilizamos o Azure Load Balancer para expor dois endereços IP frontend, um para cada website, para distribuir tráfego para a respetiva configuração IP para o website. Este cenário utiliza o mesmo número de porta em ambas as extremidades dianteiras, bem como ambos os endereços IP do pool de backend.

![Imagem de cenário LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Passos para carregar o equilíbrio em várias configurações ip

Para alcançar o cenário delineado neste artigo, complete os seguintes passos:

1. [Instale e configure o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) seguindo os passos do artigo ligado e inicie sessão na sua conta Azure.
2. [Crie um grupo](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) de recursos chamado *contosofabrikam* da seguinte forma:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Crie uma disponibilidade definida](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) para os dois VMs. Para este cenário, utilize o seguinte comando:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Crie uma rede virtual](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) chamada *myVNet* e uma subnet chamada *mySubnet:*

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Crie o equilibrador](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de carga chamado *mylb:*

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Crie dois endereços IP públicos dinâmicos para as configurações IP frontend do seu equilibrador de carga:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Crie as duas configurações IP frontend, *contosofe* e *fabrikamfe,* respectivamente:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Crie as suas piscinas de endereços de backend - *contosopool* e *fabrikampool*, uma [sonda](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, e as suas regras de equilíbrio de carga - *HTTPc* e *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Verifique a saída para [verificar se o seu equilibrador](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de carga foi criado corretamente executando o seguinte comando:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Crie um IP público,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address) *myPublicIp,* e conta de [armazenamento,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) *mystorageaccont1* para a sua primeira máquina virtual VM1 da seguinte forma:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Crie as interfaces de rede](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) para VM1 e adicione uma segunda configuração IP, *VM1-ipconfig2,* e [crie o VM](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) da seguinte forma:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Repita os passos 10-11 para o seu segundo VM:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Por fim, tem de configurar os registos de recursos DNS para apontar para o respetivo endereço IP frontal do Balancer de Carga. Pode acolher os seus domínios em Azure DNS. Para obter mais informações sobre a utilização de DNS Azure com Balancer de Carga, consulte [Utilizar o Azure DNS com outros serviços Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços de equilíbrio de carga em Azure em [Serviços de equilíbrio de carga em Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas de equilíbrio de carga em [Log analytics para O Equilíbrio de Carga Azure](../load-balancer/load-balancer-monitor-log.md).
