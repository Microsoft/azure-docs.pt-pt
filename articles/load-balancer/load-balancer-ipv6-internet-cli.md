---
title: Criar um equilibrador de carga pública com IPv6 - Azure CLI
titleSuffix: Azure Load Balancer
description: Com este caminho de aprendizagem, começa a criar um equilibrista de carga pública com o IPv6 usando o Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, balançador de carga azul, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 714fb92e8757fed6e11a09528b60cda3e945175e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218898"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Criar um equilibrista de carga pública com IPv6 usando Azure CLI

>[!NOTE] 
>Este artigo descreve uma funcionalidade introdutória do IPv6 para permitir que os Balanceadores de Carga Básica forneçam conectividade IPv4 e IPv6. A conectividade abrangente do IPv6 está agora disponível com [o IPv6 para VNETs Azure,](../virtual-network/ipv6-overview.md) que integra a conectividade IPv6 com as suas Redes Virtuais e inclui funcionalidades-chave como as regras do Grupo de Segurança da Rede IPv6, encaminhamento definido pelo utilizador IPv6, equilíbrio de carga básica e padrão IPv6, e muito mais.  IPv6 para Azure VNETs é a norma recomendada para aplicações IPv6 em Azure. Ver [IPv6 para implementação de Powershell Azure VNET](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). Os balançadores de carga proporcionam uma elevada disponibilidade distribuindo tráfego de entrada entre instâncias de serviço saudáveis em serviços de nuvem ou máquinas virtuais num conjunto de equilibradores de carga. Os equilibradores de carga também podem apresentar estes serviços em várias portas ou vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implantação de exemplo

O diagrama seguinte ilustra a solução de equilíbrio de carga que é implementada utilizando o modelo de exemplo descrito neste artigo.

![Cenário do Balanceador de carga](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Neste cenário, cria-se os seguintes recursos Azure:

* Duas máquinas virtuais (VMs)
* Uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* Um equilibrista de carga pública com um IPv4 e um endereço IP público IPv6
* Um conjunto de disponibilidade que contém os dois VMs
* Duas regras de equilíbrio de carga para mapear os VIPs públicos para os pontos finais privados

## <a name="deploy-the-solution-by-using-azure-cli"></a>Implementar a solução utilizando o Azure CLI

Os passos a seguir mostram como criar um equilibrador de carga pública utilizando o Azure CLI. Utilizando o CLI, cria-se e configura cada objeto individualmente e, em seguida, junta-os para criar um recurso.

Para implantar um equilibrador de carga, crie e configuure os seguintes objetos:

* **Configuração IP frontal**: Contém endereços IP públicos para o tráfego de rede de entrada.
* **Conjunto de endereços back-end**: Contém interfaces de rede (NICs) para que as máquinas virtuais recebam tráfego de rede do equilibrador de carga.
* **Regras de equilíbrio de carga**: Contém regras que mapeiam uma porta pública no equilibrador de carga para uma porta na piscina de endereços de fundo.
* **Regras NAT de entrada**: Contém regras de tradução de endereços de rede (NAT) que mapeiam uma porta no equilibrador de carga para uma porta para uma máquina virtual específica no pool de endereços back-end.
* **Sondas**: Contém sondas de saúde que são usadas para verificar a disponibilidade de casos de máquinas virtuais na piscina de endereços back-end.

## <a name="set-up-azure-cli"></a>Configurar a CLI do Azure

Neste exemplo, execute as ferramentas Azure CLI numa janela de comando PowerShell. Para melhorar a legibilidade e reutilização, utiliza as capacidades de script do PowerShell, não os cmdlets Azure PowerShell.

1. [Instale e configuure o CLI Azure](/cli/azure/install-azure-cli) seguindo os passos do artigo ligado e inscreva-se na sua conta Azure.

2. Configurar variáveis PowerShell para utilização com os comandos Azure CLI:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Criar um grupo de recursos, um equilibrador de carga, uma rede virtual e sub-redes

1. Criar um grupo de recursos:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Criar um equilibrador de carga:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Criar uma rede virtual:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Nesta rede virtual, crie duas sub-redes:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Criar endereços IP públicos para a piscina frontal

1. Configurar as variáveis PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Crie um endereço IP público para o pool IP frontal:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > O equilibrador de carga utiliza o rótulo de domínio do IP público como o seu nome de domínio totalmente qualificado (FQDN). Esta é uma mudança da implementação clássica, que usa o nome de serviço de nuvem como o equilibrador de carga FQDN.
    >
    > Neste exemplo, o FQDN é *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Criar piscinas frontais e traseiras

Nesta secção, você cria as seguintes piscinas IP:
* O pool IP frontal que recebe o tráfego de rede de entrada no equilibrador de carga.
* O pool IP de back-end onde a piscina frontal envia o tráfego de rede equilibrado de carga.

1. Configurar as variáveis PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Crie um pool IP frontal e associe-o ao IP público que criou no passo anterior e no equilibrador de carga.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Crie as regras da sonda, das regras do equilibrador de carga e da carga

Este exemplo cria os seguintes itens:

* Uma regra da sonda para verificar a conectividade com a porta TCP 80.
* Uma regra NAT para traduzir todo o tráfego de entrada no porto 3389 para o porto 3389 para RDP.\*
* Uma regra NAT para traduzir todo o tráfego de entrada na porta 3391 para a porta 3389 para o protocolo de ambiente de trabalho remoto (RDP).\*
* Uma regra do balançador de carga para equilibrar todo o tráfego de entrada no porto 80 para o porto 80 nos endereços da piscina traseira.

\* As regras DA Estão associadas a uma instância específica da máquina virtual por trás do equilibrador de carga. O tráfego de rede que chega à porta 3389 é enviado para a máquina virtual específica e porta que está associada à regra NAT. Tem de especificar um protocolo (UDP ou TCP) para uma regra NAT. Não pode atribuir ambos os protocolos à mesma porta.

1. Configurar as variáveis PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Cria a sonda.

    O exemplo a seguir cria uma sonda TCP que verifica a conectividade com a porta TCP de fundo 80 a cada 15 segundos. Depois de duas falhas consecutivas, marca o recurso back-end como indisponível.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Criar regras NAT de entrada que permitam ligações RDP aos recursos back-end:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Crie regras de equilíbrio de carga que enviem tráfego para diferentes portas traseiras, dependendo da extremidade frontal que recebeu o pedido.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Verifique as suas definições:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Resultado esperado:

    ```output
    info:    Executing command network lb show
    info:    Looking up the load balancer "myIPv4IPv6Lb"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
    data:    Name                            : myIPv4IPv6Lb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : southcentralus
    data:    Provisioning state              : Succeeded
    data:
    data:    Frontend IP configurations:
    data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
    data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
    data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
    data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
    data:
    data:    Probes:
    data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
    data:    -------------------  ------------------  --------  ----  ----  --------  -----
    data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
    data:
    data:    Backend Address Pools:
    data:    Name             Provisioning state
    data:    ---------------  ------------------
    data:    BackendPoolIPv4  Succeeded
    data:    BackendPoolIPv6  Succeeded
    data:
    data:    Load Balancing Rules:
    data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
    data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
    data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
    data:
    data:    Inbound NAT Rules:
    data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
    data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
    data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
    info:    network lb show
    ```

## <a name="create-nics"></a>Criar NICs

Crie NICs e associe-os com regras NAT, regras de balançadores de carga e sondas.

1. Configurar as variáveis PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Crie um NIC para cada extremidade traseira e adicione uma configuração IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Crie os recursos VM back-end e anexe cada NIC

Para criar VMs, você deve ter uma conta de armazenamento. Para o equilíbrio da carga, os VMs precisam de ser membros de um conjunto de disponibilidade. Para obter mais informações sobre a criação de VMs, consulte [Criar um VM Azure utilizando o PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Configurar as variáveis PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Este exemplo utiliza o nome de utilizador e a palavra-passe para os VMs em texto claro. Tenha o cuidado adequado quando utilizar estas credenciais em texto claro. Para obter um método mais seguro de manuseamento de credenciais em PowerShell, consulte o [`Get-Credential`](/powershell/module/microsoft.powershell.security/get-credential) cmdlet.

2. Crie o conjunto de disponibilidade:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Criar as máquinas virtuais com os NICs associados:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```