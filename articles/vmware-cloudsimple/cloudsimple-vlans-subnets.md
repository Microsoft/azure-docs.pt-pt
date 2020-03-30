---
title: VLANs e subnets em Solução Azure VMware pela CloudSimple
description: Saiba mais sobre VLANs e subredes na CloudSimple Private Cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024896"
---
# <a name="vlans-and-subnets-overview"></a>VLANs e sub-redes visão geral

A CloudSimple fornece uma rede por região onde o seu serviço CloudSimple está implantado.  A rede é um único espaço de endereço TCP Layer 3 com encaminhamento ativado por padrão.  Todas as Nuvens Privadas e subredes criadas nesta região podem comunicar entre si sem qualquer configuração adicional.  Pode criar grupos portuários distribuídos no vCenter utilizando os VLANs.

![Topologia de rede CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

Um VLAN (rede Camada 2) é criado para cada Nuvem Privada.  O tráfego da Camada 2 permanece dentro do limite de uma Nuvem Privada, permitindo-lhe isolar o tráfego local dentro da Nuvem Privada.  Um VLAN criado na Nuvem Privada pode ser usado para criar grupos portuários distribuídos apenas nessa Nuvem Privada.  Um VLAN criado numa Nuvem Privada é configurado automaticamente em todos os interruptores ligados aos anfitriões de uma Nuvem Privada.

## <a name="subnets"></a>Sub-redes

Pode criar uma sub-rede quando criar um VLAN definindo o espaço de endereço da sub-rede. Um endereço IP do espaço de endereço é atribuído como um gateway de sub-rede. Um único espaço de endereço privado camada 3 é atribuído por cliente e região. Pode configurar qualquer espaço de endereço sem sobreposição RFC 1918, com a sua rede no local ou rede virtual Azure, na sua região de rede.

Todas as subredes podem comunicar entre si por padrão, reduzindo a configuração para o encaminhamento entre nuvens privadas. Os dados do Leste-Oeste em computadores da mesma região permanecem na mesma rede de Camada 3 e transferem-se sobre a infraestrutura de rede local na região. Não é necessária uma saída para a comunicação entre nuvens privadas numa região. Esta abordagem elimina qualquer penalização de desempenho WAN/egress na implementação de diferentes cargas de trabalho em diferentes Nuvens Privadas.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN subnets CIDR range

Um ambiente Private Cloud é criado como uma pilha de VMware isolada (anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter.  Os componentes de gestão são implantados na rede selecionada para as subnets CIDR vSphere/vSAN.  A gama CIDR da rede é dividida em diferentes subredes durante a implantação.

* Prefixo mínimo de sub-prefixo de sub-prefixo de vSphere/vSAN cidr: **/24**
* Prefixo máximo de sub-nets da vSphere/vSAN CIDR: **/21**

> [!CAUTION]
> Os endereços IP na gama icdr vSphere/vSAN são reservados para utilização pela infraestrutura Cloud Privada.  Não utilize o endereço IP nesta gama em nenhuma máquina virtual.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN subnets LIMITES de gama CIDR

A seleção do tamanho da gama CIDR das subnets vSphere/vSAN tem um impacto no tamanho da sua Nuvem Privada.  A tabela que se segue mostra o número máximo de nós que pode ter com base no tamanho das subnets cidr vSphere/vSAN.

| VSphere/vSAN especificado subfixa comprimento de prefixo CIDR | Número máximo de nós |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Subnets de gestão criadas numa Nuvem Privada

As seguintes subredes de gestão são criadas quando se cria uma Nuvem Privada.

* **Gestão do sistema.** VLAN e subnet para a rede de gestão dos anfitriões ESXi, servidor DNS, servidor vCenter.
* **VMotion.** VLAN e subnet para a rede vMotion dos anfitriões ESXi.
* **VSAN.** VLAN e subnet para a rede vSAN dos anfitriões ESXi.
* **NsxtEdgeUplink1**. VLAN e subnet para ligações VLAN a uma rede externa.
* **NsxtEdgeUplink2**. VLAN e subnet para ligações VLAN a uma rede externa.
* **NsxtEdgeTransport**. VLAN e subnet para zonas de transporte controlam o alcance das redes Camada 2 em NSX-T.
* **NsxtHostTransport**. VLAN e subnet para zona de transporte de hospedeiro.

### <a name="management-network-cidr-range-breakdown"></a>Desagregação da gama CIDR da rede de gestão

vSphere/vSAN subnets CIDR intervalo especificado é dividido em várias subredes.  A tabela que se segue mostra um exemplo da avaria para os prefixos permitidos.  O exemplo utiliza 192.168.0.0 como gama CIDR.

Exemplo:

| VSphere/vSAN especificado subnets CIDR/prefixo | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestão do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMoção | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transporte hospedeiro NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte nsx-t borda | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Uplink 2 de borda NSX-T | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir VLANs e sub-redes](create-vlan-subnet.md)
