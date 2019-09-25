---
title: Práticas recomendadas de rede Service Fabric do Azure | Microsoft Docs
description: Práticas recomendadas para gerenciar Service Fabric rede.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 317977af9d41163013545a6e5f60bee887da596c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262255"
---
# <a name="networking"></a>Redes

Conforme você cria e gerencia clusters do Azure Service Fabric, você está fornecendo conectividade de rede para seus nós e aplicativos. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, balanceadores de carga e grupos de segurança de rede. Neste artigo, você aprenderá as práticas recomendadas para esses recursos.

Examine os [padrões de rede Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) do Azure para saber como criar clusters que usam os seguintes recursos: Rede virtual ou sub-rede existente, endereço IP público estático, balanceador de carga somente interno ou balanceador de carga interno e externo.

## <a name="infrastructure-networking"></a>Rede de infraestrutura
Maximize o desempenho da sua máquina virtual com rede acelerada, declarando a propriedade enableAcceleratedNetworking em seu modelo do Resource Manager, o trecho a seguir é de um NetworkInterfaceConfigurations do conjunto de dimensionamento de máquinas virtuais que habilita a rede acelerada:

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
Service Fabric cluster pode ser provisionado no [Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)e [Windows com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

A rede acelerada tem suporte para SKUs da série de máquinas virtuais do Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e MS/MMS. A rede acelerada foi testada com êxito usando o SKU Standard_DS8_v3 em 1/23/2019 para um Cluster Service Fabric Windows e usando Standard_DS12_v2 em 01/29/2019 para um Cluster Service Fabric Linux.

Para habilitar a rede acelerada em um cluster existente do Service Fabric, você precisa primeiro [dimensionar um cluster de Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), para executar o seguinte:
1. Provisionar um NodeType com rede acelerada habilitada
2. Migre seus serviços e seu estado para o NodeType provisionado com rede acelerada habilitada

A infraestrutura de expansão é necessária para habilitar a rede acelerada em um cluster existente, pois a habilitação da rede acelerada em vigor causaria tempo de inatividade, pois requer que todas as máquinas virtuais em um conjunto de disponibilidade sejam [interrompidas e desalocadas antes Habilitando a rede acelerada em qualquer NIC existente](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Rede de cluster

* Service Fabric clusters podem ser implantados em uma rede virtual existente seguindo as etapas descritas em [Service Fabric padrões de rede](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* NSGs (grupos de segurança de rede) são recomendados para tipos de nós que restringem o tráfego de entrada e de saída para seu cluster. Verifique se as portas necessárias estão abertas no NSG. Por exemplo: ![Service Fabric regras NSG][NSGSetup]

* O tipo de nó primário, que contém os serviços do sistema Service Fabric não precisa ser exposto por meio do balanceador de carga externo e pode ser exposto por um [balanceador de carga interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Use um [endereço IP público estático](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) para o cluster.

## <a name="application-networking"></a>Rede de aplicativos

* Para executar cargas de trabalho de contêiner do Windows, use o [modo de rede aberto](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) para facilitar a comunicação entre serviços.

* Use um proxy reverso, como [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) , ou o [Service Fabric proxy reverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para expor portas de aplicativo comuns, como 80 ou 443.

* Para contêineres do Windows hospedados em computadores gapped que não podem efetuar pull de camadas base do armazenamento em nuvem do Azure, substitua o comportamento da camada estrangeira usando o sinalizador [--Allow-unredistributable-artefatos](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) no daemon do Docker.

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Service Fabric a criação do cluster para Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
