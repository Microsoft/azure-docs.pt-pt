---
title: Redes de tecido de serviço Azure
description: Boas práticas e considerações de design para gerir a conectividade da rede utilizando o Tecido de Serviço Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551799"
---
# <a name="networking"></a>Redes

À medida que cria e gere os clusters Azure Service Fabric, está a fornecer conectividade de rede para os seus nós e aplicações. Os recursos de rede incluem gamas de endereços IP, redes virtuais, equilibradores de carga e grupos de segurança de rede. Neste artigo, você aprenderá as melhores práticas para estes recursos.

Reveja os padrões de rede de [tecidos](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) de serviço Azure para aprender a criar clusters que utilizam as seguintes funcionalidades: rede virtual ou subnet existente, endereço IP público estático, balanceador de carga interna ou equilibrista de carga interna e externo.

## <a name="infrastructure-networking"></a>Networking de Infraestruturas
Maximize o desempenho da sua Máquina Virtual com Networking Acelerado, declarando a propriedade enabledNetworking no seu modelo de Gestor de Recursos, o seguinte snippet é de uma Rede de Configurações de Configurações de Conjunto de Escala de Máquina Virtual que permite a ligação acelerada:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
O cluster de tecido de serviço pode ser aprovisionado em [Linux com Networking Acelerado](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)e [Windows com Networking Acelerado](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

A Rede Acelerada é suportada para as Séries Automáticas Virtuais Azure SKUs: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. A Rede Acelerada foi testada com sucesso utilizando o Standard_DS8_v3 SKU em 1/23/2019 para um Cluster de Janelas de Tecido de Serviço, e usando Standard_DS12_v2 em 01/29/2019 para um Cluster De Linux de Tecido de Serviço.

Para ativar a rede acelerada num cluster de tecido de serviço existente, é necessário primeiro dimensionar um cluster de tecido de [serviço, adicionando um conjunto](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)de escala de máquina virtual, para executar o seguinte:
1. Provision arrestão de um Nó com Rede Acelerada ativada
2. Migrar os seus serviços e o seu estado para o nó provisionado com networking acelerado habilitado

É necessária uma infraestrutura de dimensionamento para permitir que a Rede Acelerada num cluster existente, porque permitir a rede acelerada no lugar causaria tempo de inatividade, uma vez que requer que todas as máquinas virtuais num conjunto de disponibilidade sejam [paradas e desalocadas antes de permitir a rede acelerada em qualquer NIC existente.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)

## <a name="cluster-networking"></a>Cluster Networking

* Os clusters de tecido de serviço podem ser implantados numa rede virtual existente seguindo os passos delineados nos [padrões](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)de rede service Fabric .

* Os grupos de segurança da rede (NSGs) são recomendados para tipos de nó que restringem o tráfego de entrada e saída ao seu cluster. Certifique-se de que as portas necessárias são abertas no NSG. Por exemplo: ![Regras NSG de tecido de serviço][NSGSetup]

* O tipo principal do nó, que contém os serviços do sistema Service Fabric, não precisa de ser exposto através do equilibrador de carga externo e pode ser exposto por um [equilibrador de carga interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Utilize um [endereço IP público estático](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) para o seu cluster.

## <a name="application-networking"></a>Rede de aplicações

* Para executar as cargas de trabalho do windows, utilize o modo de [rede aberto](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) para facilitar a comunicação serviço-a-serviço.

* Utilize um proxy inverso como [traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) ou o proxy inverso do [Tecido de Serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para expor portas de aplicação comuns como 80 ou 443.

* Para os Recipientes Windows hospedados em máquinas com ar-gapped que não conseguem puxar camadas base do armazenamento em nuvem Azure, sobrepor-se ao comportamento da camada estrangeira, utilizando a bandeira de artefactos não [distribuíveis](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) no daemon do Docker.

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação](service-fabric-cluster-creation-for-windows-server.md) de cluster de tecido de serviço para Windows Server
* Criar um cluster em VMs ou computadores em execução linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
