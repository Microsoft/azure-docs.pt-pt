---
title: VLANs e subredes na solução de VMware ao CloudSimple - Azure
description: Saiba mais sobre VLANs e sub-redes numa nuvem privada CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d29445054848d798476fed8184b89f9b6c1210f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497562"
---
# <a name="vlans-and-subnets-overview"></a>Descrição geral de VLANs e sub-redes

CloudSimple fornece uma rede por região em que o seu serviço CloudSimple é implementado.  A rede é um único espaço de endereço TCP camada 3 com o encaminhamento ativado por predefinição.  Todas as nuvens privadas e as sub-redes criadas nesta região podem comunicar entre si sem qualquer configuração adicional.  Pode criar grupos de portas distribuída no vCenter com as VLANs.

![Topologia de rede CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

VLANs (rede de camada 2) são criados por nuvem privada.  O tráfego de camada 2 permanece dentro do limite de uma nuvem privada, permitindo-lhe isolar o tráfego local na nuvem privada.  Uma VLAN criada na cloud privada pode ser utilizada para criar grupos de portas distribuídas apenas nessa nuvem privada.  Uma VLAN criada numa nuvem privada é configurada automaticamente em todos os comutadores ligados aos anfitriões de uma nuvem privada.

## <a name="subnets"></a>Sub-redes

Pode criar uma sub-rede quando criar uma VLAN, definindo o espaço de endereços da sub-rede. É atribuído um endereço IP do espaço de endereço como um gateway de sub-rede. Um único espaço de endereços de camada 3 privado é atribuído por cliente e a região. Pode configurar o espaço de endereço não sobrepostos qualquer RFC 1918, com a sua rede no local ou a rede virtual do Azure, na sua região de rede.

Todas as sub-redes podem comunicar entre si por predefinição, reduzindo a configuração de sobrecarga de encaminhamento entre nuvens privadas. Este-Oeste dados em PCs na mesma região permanecem na mesma rede de camada 3 e das transferências de durante a infraestrutura de rede local dentro da região. Nenhuma saída é necessária para a comunicação entre clouds privadas numa região. Essa abordagem elimina quaisquer penalidade de desempenho de WAN/saída na implantação de diferentes cargas de trabalho em nuvens privadas diferentes.

## <a name="vspherevsan-subnets-cidr-range"></a>intervalo CIDR do vSphere/vSAN sub-redes

Uma nuvem privada é criada como uma pilha de VMware isolada (anfitriões ESXi, vCenter, vSAN e NSX) ambiente gerido por um vCenter server.  Componentes de gerenciamento são implementados numa rede selecionada para **vSphere/vSAN sub-redes CIDR**.  A intervalo CIDR de rede é dividida em sub-redes diferentes durante a implantação.

Prefixo de intervalo de sub-redes de vSphere/vSAN CIDR mínimo: **/24** prefixo de intervalo de sub-redes de vSphere/vSAN máximo CIDR:   **/21**

> [!CAUTION]
> Endereços IP no intervalo CIDR vSphere/vSAN estão reservados para utilização pela infraestrutura de nuvem privada. Não utilize um endereço IP neste intervalo em qualquer máquina virtual.


### <a name="vspherevsan-subnets-cidr-range-limits"></a>limites de intervalo de CIDR do vSphere/vSAN sub-redes

Selecionar o tamanho do intervalo CIDR de sub-redes de vSphere/vSAN tem um impacto no tamanho da sua nuvem privada.  Tabela a seguir mostra o número máximo de nós que pode com base no tamanho do vSphere/vSAN sub-redes CIDR.

| Comprimento do prefixo especificadas vSphere/vSAN sub-redes CIDR | Número máximo de nós |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Sub-redes de gestão criados numa nuvem privada

Seguintes sub-redes de gestão são criadas quando criar uma nuvem privada. 

* **Gestão do sistema** -VLAN e sub-rede para a gestão dos anfitriões ESXi de rede, servidor DNS, servidor vCenter.
* **VMotion** -VLAN e sub-rede para a rede de vMotion dos anfitriões ESXi.
* **VSAN** -VLAN e sub-rede para a rede de vSAN dos anfitriões ESXi.
* **NsxtEdgeUplink1** -VLAN e sub-rede para uplinks VLAN para uma rede externa.
* **NsxtEdgeUplink2** -VLAN e sub-rede para uplinks VLAN para uma rede externa.
* **NsxtEdgeTransport** -VLAN e sub-rede para zonas de transporte, controlar o alcance das redes de camada 2 no NSX-T.
* **NsxtHostTransport** -zona de transporte de VLAN e sub-rede para o anfitrião.

### <a name="management-network-cidr-range-breakdown"></a>Divisão de intervalo CIDR de rede de gestão

intervalo CIDR de sub-redes de vSphere/vSAN especificado está dividido em várias sub-redes.  Tabela a seguir mostra um exemplo de divisão de prefixos permitidos.  O exemplo usa **192.168.0.0** como o intervalo CIDR.

Exemplo:

| Especificar sub-redes de vSphere/vSAN/prefixo CIDR | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestão do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transporte de anfitrião NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte de borda NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passos Seguintes

* [Criar e gerir VLANs e sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
