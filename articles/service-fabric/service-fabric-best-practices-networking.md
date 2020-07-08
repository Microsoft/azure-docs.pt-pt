---
title: Azure Service Fabric networking as melhores práticas
description: Boas práticas e considerações de design para gerir a conectividade da rede utilizando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75551799"
---
# <a name="networking"></a>Redes

À medida que cria e gere os clusters Azure Service Fabric, está a fornecer conectividade de rede para os seus nós e aplicações. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, equilibradores de carga e grupos de segurança de rede. Neste artigo, aprenderá as melhores práticas para estes recursos.

Reveja [os Padrões de Rede de Tecidos de Serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) Azure para aprender a criar clusters que utilizem as seguintes funcionalidades: Rede virtual ou sub-rede existentes, endereço IP público estático, equilibr de carga interno ou equilibrador de carga interno e externo.

## <a name="infrastructure-networking"></a>Rede de Infraestruturas
Maximize o desempenho da sua Máquina Virtual com rede acelerada, declarando ativação da propriedade Denetworking ativada no seu modelo de Gestor de Recursos, o seguinte corte é de um Conjunto de Escala de Máquina Virtual Configurar Configurações que permite networking acelerado:

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
O cluster de tecido de serviço pode ser a provisionado em [Linux com Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)e [Windows com Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

A Rede Acelerada é suportada para SKUs da Série de Máquinas Virtuais Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. O Accelerated Networking foi testado com sucesso utilizando o SKU Standard_DS8_v3 em 1/23/2019 para um Cluster de Janelas de Tecido de Serviço, e utilizando Standard_DS12_v2 em 01/29/2019 para um Cluster de Tecido de Serviço Linux.

Para ativar o networking acelerado num cluster de tecido de serviço existente, é necessário [primeiro escalar um cluster de tecido de serviço adicionando um Conjunto de Balança de Máquina Virtual,](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)para executar o seguinte:
1. Provisionar um NodeType com rede acelerada ativada
2. Migrar os seus serviços e o seu estado para o NodeType provisionado com rede acelerada habilitado

A escala da infraestrutura é necessária para permitir a rede acelerada num cluster existente, pois permitir uma rede acelerada no local causaria tempo de inatividade, uma vez que requer que todas as máquinas virtuais num conjunto de disponibilidade sejam [paradas elocadas antes de permitir a ligação de rede acelerada em qualquer NIC existente.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)

## <a name="cluster-networking"></a>Rede de Agrupamentos

* Os clusters de tecido de serviço podem ser implantados numa rede virtual existente, seguindo os passos descritos nos [padrões de networking do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Os grupos de segurança da rede (NSGs) são recomendados para os tipos de nó que restringem o tráfego de entrada e saída ao seu cluster. Certifique-se de que as portas necessárias são abertas no NSG. Por exemplo: ![ Regras NSG do tecido de serviço][NSGSetup]

* O tipo de nó primário, que contém os serviços do sistema Service Fabric, não precisa de ser exposto através do balançador de carga externo e pode ser exposto por um [equilibrador de carga interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Utilize um [endereço IP público estático](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) para o seu cluster.

## <a name="application-networking"></a>Rede de aplicações

* Para executar as cargas de trabalho dos contentores do Windows, utilize [o modo de rede aberto](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) para facilitar a comunicação de serviço ao serviço.

* Utilize um representante inverso, como [o Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) ou o [proxy reverso do Tecido de Serviço,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para expor portas de aplicação comuns como 80 ou 443.

* Para os recipientes windows alojados em máquinas com lacunas de ar que não conseguem puxar camadas de base do armazenamento em nuvem Azure, sobreponsem o comportamento da camada estrangeira, utilizando a bandeira [de artefactos não-tristribuíveis](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) no daemon do Docker.

## <a name="next-steps"></a>Próximos passos

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para windows server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
