---
title: O Azure Service Fabric, melhores práticas de rede | Documentos da Microsoft
description: Melhores práticas para o gerenciamento de sistema de rede do Service Fabric.
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
ms.openlocfilehash: 86ad6fce34f323d94f7b9c318ba81f547360d4df
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804968"
---
# <a name="networking"></a>Redes

Como criar e gerir clusters do Azure Service Fabric, está a fornecer conectividade de rede para os nós e as aplicações. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, balanceadores de carga e grupos de segurança de rede. Neste artigo, aprenderá as práticas recomendadas para estes recursos.

Reveja as do Azure [padrões de redes de recursos de infraestrutura do serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) para saber como criar clusters que utilizam as seguintes funcionalidades: Balanceador de carga de rede virtual existente ou sub-rede, endereço IP público estático, Balanceador de carga interno só, ou interno e externo.

## <a name="infrastructure-networking"></a>Redes de infraestrutura
Maximizar o desempenho da sua máquina Virtual com redes aceleradas, ao declarar enableAcceleratedNetworking propriedade no modelo do Resource Manager, o trecho a seguir é de um NetworkInterfaceConfigurations de definir de dimensionamento da Máquina Virtual que permite que o Accelerated Networking:

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
Cluster do Service Fabric pode ser provisionado em [Linux com redes aceleradas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), e [Windows com redes aceleradas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Funcionamento em rede acelerado é suportado para SKUs de série de Máquina Virtual do Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. Funcionamento em rede acelerado foi testado com êxito com o SKU de Standard_DS8_v3 1/23/2019 para um Cluster Windows do Service Fabric e o uso Standard_DS12_v2 01/29/2019 para um Linux Cluster do Service Fabric.

Para ativar o Accelerated Networking num cluster do Service Fabric existente, precisa primeiro [dimensionar um cluster do Service Fabric out ao adicionar um conjunto de dimensionamento de Máquina Virtual](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), para efetuar o seguinte:
1. Aprovisionar um NodeType com redes aceleradas ativada
2. Migrar seus serviços e o respetivo estado para o NodeType aprovisionado com redes aceleradas ativada

Ampliar a infraestrutura é necessária para ativar o Accelerated Networking num cluster existente, uma vez que ativar redes aceleradas em vigor faria com que o tempo de inatividade, à medida que ele necessita de ser de todas as máquinas virtuais num conjunto de disponibilidade [parar e desalocar antes de ativar redes aceleradas em qualquer NIC existente](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Redes do cluster

* Clusters do Service Fabric podem ser implementados numa rede virtual existente ao seguir os passos descritos em [padrões de rede do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Grupos de segurança de rede (NSGs) são recomendados para tipos de nós que restringem o tráfego de entrada e saído para o seu cluster. Certifique-se de que as portas necessárias estão abertas no NSG. Por exemplo: ![Regras NSG do Service Fabric][NSGSetup]

* O tipo de nó principal, que contém os serviços de sistema do Service Fabric não precisa de ser exposto através do Balanceador de carga externo e pode ser exposto por um [Balanceador de carga interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Utilize um [endereço IP público estático](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) para o seu cluster.

## <a name="application-networking"></a>Rede de aplicação

* Para executar cargas de trabalho do Windows contentor, utilize [abrir o modo de funcionamento em rede](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) para facilitar a comunicação de serviço para serviço.

* Utilizar como um proxy inverso [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) ou o [proxy inverso do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para expor portas de aplicação comuns, como 80 ou 443.

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
