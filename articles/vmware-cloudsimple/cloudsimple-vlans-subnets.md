---
title: VLANs e sub-redes em Azure VMware Solution by CloudSimple
description: Saiba mais sobre VLANs e sub-redes em CloudSimple Private Cloud e sobre a rede que o CloudSimple fornece onde o seu serviço CloudSimple está implantado.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140671"
---
# <a name="vlans-and-subnets-overview"></a>VLANs e sub-redes visão geral

O CloudSimple fornece uma rede por região onde o seu serviço CloudSimple está implantado.  A rede é um único espaço de endereço TCP Layer 3 com encaminhamento ativado por padrão.  Todas as Nuvens Privadas e subesí redes criadas nesta região podem comunicar-se entre si sem qualquer configuração adicional.  Pode criar grupos porta distribuídos no vCenter utilizando os VLANs.

![Topologia da Rede CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

Uma VLAN (rede Camada 2) é criada para cada Nuvem Privada.  O tráfego da Camada 2 permanece dentro do limite de uma Nuvem Privada, permitindo-lhe isolar o tráfego local dentro da Nuvem Privada.  Um VLAN criado na Nuvem Privada pode ser usado para criar grupos portuários distribuídos apenas nessa Nuvem Privada.  Um VLAN criado numa Nuvem Privada é configurado automaticamente em todos os interruptores ligados aos anfitriões de uma Nuvem Privada.

## <a name="subnets"></a>Sub-redes

Pode criar uma sub-rede quando criar um VLAN definindo o espaço de endereço da sub-rede. Um endereço IP a partir do espaço de endereço é atribuído como um gateway de sub-rede. Um único espaço de endereço privado Camada 3 é atribuído por cliente e região. Pode configurar qualquer espaço de endereço não sobreposto RFC 1918, com a sua rede no local ou rede virtual Azure, na sua região de rede.

Todas as sub-redes podem comunicar entre si por defeito, reduzindo a sobrecarga de configuração para encaminhamento entre Nuvens Privadas. Os dados leste-oeste através dos computadores da mesma região permanecem na mesma rede da Camada 3 e transferem-se sobre a infraestrutura de rede local na região. Não é necessária saída para a comunicação entre nuvens privadas numa região. Esta abordagem elimina qualquer penalização de desempenho WAN/egress na implementação de diferentes cargas de trabalho em diferentes Nuvens Privadas.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN sub-redes CIDR gama

Uma Nuvem Privada é criada como um ambiente isolado de pilha de VMware (anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter.  Os componentes de gestão são implantados na rede selecionada para sub-redes CIDR vSphere/vSAN.  A gama CIDR de rede é dividida em diferentes sub-redes durante a implantação.

* Mínimo vSphere/vSAN sub-redes CIDR prefixo de gama CIDR: **/24**
* Máximo vSphere/vSAN sub-redes CIDR prefixo de gama CIDR: **/21**

> [!CAUTION]
> Os endereços IP na gama vSphere/vSAN CIDR são reservados para utilização pela infraestrutura Private Cloud.  Não utilize o endereço IP nesta gama em qualquer máquina virtual.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN sub-redes limites de gama CIDR

A seleção do tamanho da gama CIDR das sub-redes vSphere/vSAN tem um impacto no tamanho da sua Nuvem Privada.  A tabela a seguir mostra o número máximo de nós que pode ter com base no tamanho das sub-redes CIDR vSphere/vSAN.

| Especificado vSphere/vSAN sub-redes comprimento do prefixo CIDR | Número máximo de nós |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Subesí redes de gestão criadas numa Nuvem Privada

As seguintes sub-redes de gestão são criadas quando cria uma Nuvem Privada.

* **Gestão do sistema.** VLAN e sub-rede para a rede de gestão dos anfitriões ESXi, servidor DNS, servidor vCenter.
* **VMotion**. VLAN e sub-rede para a rede vMotion dos anfitriões ESXi.
* **VSAN.** VLAN e sub-rede para a rede vSAN dos anfitriões da ESXi.
* **NsxtEdgeUplink1**. VLAN e sub-rede para ligações VLAN a uma rede externa.
* **NsxtEdgeUplink2**. VLAN e sub-rede para ligações VLAN a uma rede externa.
* **NsxtEdgeTransport**. VLAN e sub-rede para zonas de transporte controlam o alcance das redes da Camada 2 em NSX-T.
* **NsxtHostTransport**. VLAN e sub-rede para zona de transporte de hospedeiro.

### <a name="management-network-cidr-range-breakdown"></a>Quebra de gama CIDR de rede de gestão

vSphere/vSAN sub-redes CIDR gama especificada é dividida em várias sub-redes.  A tabela a seguir mostra um exemplo da avaria para prefixos permitidos.  O exemplo utiliza 192.168.0.0 como gama CIDR.

Exemplo:

| Sub-redes especificadas vSphere/vSAN CIDR/prefixo | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestão do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transporte de anfitriões NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte NSX-T Edge | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Uplink NSX-T Edge2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir VLANs e sub-redes](create-vlan-subnet.md)
