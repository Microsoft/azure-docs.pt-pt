---
title: Criar um balanceador de carga interno – CLI clássica do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga interno com a CLI clássica do Azure no modelo de implementação clássica
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c0b061da308f2e8635cf1722cb6a735624501374
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090984"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Introdução à criação de um balanceador de carga interno com a CLI clássica do Azure

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Serviços cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Para criar um conjunto de balanceadores de carga internos para máquinas virtuais

Para criar um conjunto de balanceadores de carga internos e os servidores que enviam o tráfego para o mesmo, tem de fazer o seguinte:

1. Crie uma instância de Balanceamento de Carga Interno que é ponto final do tráfego de entrada para ser sujeito a balanceamento de carga nos servidores de um conjunto com balanceamento de carga.
2. Adicione os pontos finais correspondentes às máquinas virtuais que podem receber o tráfego de entrada.
3. Configure os servidores para enviar o respetivo tráfego para o endereço IP virtual (VIP) da instância de Balanceamento de Carga Interno.

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>Criação passo a passo de um balanceador de carga interno com a CLI clássica

Este guia mostra como criar um balanceador de carga interno com base no cenário acima.

1. Se nunca tiver utilizado a CLI clássica, veja [Instalar e Configurar a CLI do Azure](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo clássico, como mostrado abaixo.

    ```azurecli
    azure config mode asm
    ```

    Resultado esperado:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Criar o ponto final e o conjunto de balanceadores de carga

O cenário assume as máquinas virtuais "DB1" e "DB2" num serviço em nuvem denominado "mytestcloud". Ambas as máquinas virtuais estão a utilizar uma rede virtual denominada "mytestvnet" com a sub-rede "subnet-1".

Este guia cria um conjunto de balanceadores de carga internos com a porta 1433 como porta privada e a porta 1433 como porta local.

Este é um cenário comum em que tem máquinas virtuais SQL no back-end com um balanceador de carga interno para garantir que os servidores de base de dados não são expostos diretamente através de um endereço IP público.

### <a name="step-1"></a>Passo 1

Criar um conjunto de balanceadores de carga internos com `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Veja `azure service internal-load-balancer --help` para obter mais informações.

Pode verificar as propriedades do balanceador de carga interno através do comando `azure service internal-load-balancer list` *nome do serviço em nuvem*.

Segue-se um exemplo do resultado:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Passo 2

Configure o conjunto de balanceadores de carga internos quando adicionar o primeiro ponto final. Pode associar o ponto final, a máquina virtual e a porta de sonda ao conjunto de balanceadores de carga internos neste passo.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Passo 3

Verifique a configuração do balanceador de carga através de `azure vm show` *nome da máquina virtual*

```azurecli
azure vm show DB1
```

O resultado é o seguinte:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto final de ambiente de trabalho remoto para uma máquina virtual

Pode criar um ponto final de ambiente de trabalho remoto para reencaminhar o tráfego de rede de uma porta pública para uma porta local de uma máquina virtual específica através de `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Remover uma máquina virtual do balanceador de carga

Pode remover uma máquina virtual de um conjunto de balanceadores de carga internos ao eliminar o ponto final associado. Após a remoção do ponto final, a máquina virtual já não pertence ao conjunto de balanceadores de carga.

Através do exemplo acima, pode remover o ponto final criado para a máquina virtual "DB1" do balanceador de carga interno "ilbset" com o comando `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Veja `azure vm endpoint --help` para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

[Configurar um modo de distribuição de balanceador de carga com a afinidade do IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
