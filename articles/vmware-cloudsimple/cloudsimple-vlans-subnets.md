---
title: VLANs e subredes em Soluções Azure VMware (AVS)
description: Saiba mais sobre VLANs e subredes em AVS Private Cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d0ce15c782ae70e16f55a28ec8c4b70f3b080f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024896"
---
# <a name="vlans-and-subnets-overview"></a>Visão geral de VLANs e sub-redes

A AVS fornece uma rede por região onde o seu serviço AVS é implantado. A rede é um único espaço de endereço de camada TCP 3 com roteamento habilitado por padrão. Todas as Nuvens Privadas aVS e subredes criadas nesta região podem comunicar entre si sem qualquer configuração adicional. Você pode criar grupos de portas distribuídas no vCenter usando as VLANs.

![Topologia da Rede AVS](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

Um VLAN (rede Camada 2) é criado para cada Nuvem Privada AVS. O tráfego da Camada 2 permanece dentro do limite de uma Nuvem Privada AVS, permitindo-lhe isolar o tráfego local dentro da Nuvem Privada AVS. Um VLAN criado na Nuvem Privada AVS pode ser usado para criar grupos portuários distribuídos apenas naquela Nuvem Privada AVS. Um VLAN criado numa Nuvem Privada AVS é configurado automaticamente em todos os interruptores ligados aos anfitriões de uma Nuvem Privada AVS.

## <a name="subnets"></a>Sub-redes

Você pode criar uma sub-rede ao criar uma VLAN definindo o espaço de endereço da sub-rede. Um endereço IP do espaço de endereço é atribuído como um gateway de sub-rede. Um único espaço de endereço de camada privada 3 é atribuído por cliente e região. Você pode configurar qualquer espaço de endereço não sobreposto RFC 1918, com sua rede local ou rede virtual do Azure, na sua região de rede.

Todas as subredes podem comunicar entre si por padrão, reduzindo a configuração para o encaminhamento entre Nuvens Privadas AVS. Os dados East-oeste em PCs na mesma região permanecem na mesma rede de camada 3 e são transferidos pela infraestrutura de rede local dentro da região. Não é necessária nenhuma saída para a comunicação entre as Nuvens Privadas da AVS numa região. Esta abordagem elimina qualquer penalização de desempenho WAN/egress na implementação de diferentes cargas de trabalho em diferentes Nuvens Privadas AVS.

## <a name="vspherevsan-subnets-cidr-range"></a>intervalo de CIDR de sub-redes vSphere/vSAN

Um ambiente AVS Private Cloud é criado como uma pilha de VMware isolada (anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter. Os componentes de gerenciamento são implantados na rede selecionada para o CIDR de sub-redes vSphere/vSAN. O intervalo de CIDR de rede é dividido em sub-redes diferentes durante a implantação.

* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN mínima: **/24**
* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN máxima: **/21**

> [!IMPORTANT]
> Os endereços IP na gama vSphere/vSAN CIDR são reservados para utilização pela infraestrutura AVS Private Cloud. Não use o endereço IP nesse intervalo em qualquer máquina virtual.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limites de intervalo CIDR de sub-redes vSphere/vSAN

A seleção do tamanho da gama CIDR das subnets vSphere/vSAN tem um impacto no tamanho da sua Nuvem Privada AVS. A tabela a seguir mostra o número máximo de nós que você pode ter com base no tamanho do CIDR de sub-redes vSphere/vSAN.

| Comprimento do prefixo CIDR de sub-redes vSphere/vSAN especificadas | Número máximo de nós |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-an-avs-private-cloud"></a>Subnets de gestão criadas numa Nuvem Privada AVS

As seguintes subredes de gestão são criadas quando cria uma Nuvem Privada AVS.

* **Gerenciamento do sistema**. VLAN e sub-rede para os hosts ESXi rede de gerenciamento, servidor DNS, servidor vCenter.
* **VMotion**. VLAN e sub-rede para ESXi hospeda a rede do ' vMotion '.
* **VSAN**. VLAN e sub-rede para a rede vSAN de hosts ESXi.
* **NsxtEdgeUplink1**. VLAN e sub-rede para uplinks de VLAN para uma rede externa.
* **NsxtEdgeUplink2**. VLAN e sub-rede para uplinks de VLAN para uma rede externa.
* **NsxtEdgeTransport**. VLAN e sub-rede para zonas de transporte controlam o alcance de redes de camada 2 no NSX-T.
* **NsxtHostTransport**. VLAN e sub-rede para zona de transporte de host.

### <a name="management-network-cidr-range-breakdown"></a>Divisão de intervalo CIDR de rede de gerenciamento

o intervalo CIDR de sub-redes vSphere/vSAN especificado é dividido em várias sub-redes. A tabela a seguir mostra um exemplo da divisão para prefixos permitidos. O exemplo usa 192.168.0.0 como o intervalo CIDR.

Exemplo:

| CIDR/prefixo de sub-redes vSphere/vSAN especificadas | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gerenciamento do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Servidor de transporte do NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte de borda do NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerenciar VLANs e sub-redes](create-vlan-subnet.md)
