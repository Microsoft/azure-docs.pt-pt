---
title: Criar um balanceador de carga com acesso à Internet – CLI clássica do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga com acesso à Internet num modelo de implementação clássica com a CLI clássica do Azure
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fa89117e85bc3d3c9664e6aa037fac923b7432ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544896"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Introdução à criação de um balanceador de carga (clássico) de acesso à Internet na CLI do Azure clássica

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI clássica do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Serviços em Nuvem do Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: O Azure Resource Manager e clássica. Confirme que compreende os [modelos e ferramentas de implementação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas clicando nos separadores na parte superior deste artigo. Este artigo abrange o modelo de implementação clássica. Também pode [saber como criar um balanceador de carga com acesso à Internet com o Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Criar um balanceador de carga com acesso à Internet com a CLI

Este guia mostra como criar um balanceador de carga com acesso à Internet com base no cenário acima.

1. Se nunca tiver utilizado a CLI clássica do Azure, veja [Instalar e Configurar a CLI clássica do Azure](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo clássico, como mostrado abaixo.

    ```azurecli
    azure config mode asm
    ```

    Resultado esperado:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Criar o ponto final e o conjunto de balanceadores de carga

O cenário pressupõe que foram criadas as máquinas virtuais "web1" e "web2".
Este guia irá criar um conjunto de balanceadores de carga através da porta 80 como porta pública e a porta 80 como porta local. Uma porta de sonda também é configurada na porta 80 e é atribuído ao conjunto de balanceadores de carga o nome "lbset".

### <a name="step-1"></a>Passo 1

Crie o primeiro ponto final e carregue o conjunto de balanceadores de carga através de `azure network vm endpoint create` para a máquina virtual "web1".

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Passo 2

Adicione uma segunda máquina virtual "web2" para o conjunto de balanceadores de carga.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Passo 3

Verifique a configuração do balanceador de carga através de `azure vm show` .

```azurecli
azure vm show web1
```

O resultado será:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto final de ambiente de trabalho remoto para uma máquina virtual

Pode criar um ponto final de ambiente de trabalho remoto para reencaminhar o tráfego de rede de uma porta pública para uma porta local de uma máquina virtual específica através de `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Remover uma máquina virtual do balanceador de carga

Tem de eliminar o ponto final associado ao conjunto de balanceadores de carga da máquina virtual. Após a remoção do ponto final, a máquina virtual já não pertence ao conjunto de balanceadores de carga.

Através do exemplo acima, pode remover o ponto final criado para a máquina virtual "web1" do balanceador de carga "lbset" com o comando `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> Pode explorar mais opções para gerir pontos finais com o comando `azure vm endpoint --help`

## <a name="next-steps"></a>Passos Seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
