---
title: Soluções Azure VMware (AVS) - Componentes VMware em nuvem privada AVS
description: Descreve como os componentes VMware são instalados na nuvem privada AVS
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016787"
---
# <a name="avs-private-cloud-vmware-components"></a>Componentes VMware De Nuvem Privada AVS

Um ambiente AVS Private Cloud é uma pilha de VMware isolada (anfitriões ESXi, vCenter, vSAN e NSX) gerida por um servidor vCenter num domínio de gestão. O serviço AVS permite-lhe implantar VMware de forma nativa em infraestruturas metálicas azure em localizações Azure. As Nuvens Privadas AVS estão integradas com o resto da Nuvem Azure. Uma Nuvem Privada AVS é implantada com os seguintes componentes de pilha VMware:

* **VMware ESXi-** Hipervisor em nós dedicados do Azure
* **VMware vCenter -** Aparelho para gestão centralizada do ambiente AVS Private Cloud vSphere
* **VSAN do VMware-** Solução de infraestrutura hiperconvergente
* **Data Center do VMware NSX-** Virtualização de rede e software de segurança  

## <a name="vmware-component-versions"></a>Versões de componentes do VMware

Uma pilha de VMware AVS Private Cloud é implementada com a seguinte versão de software.

| Componente | Versão | Versão licenciada |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | vCenter padrão |
| vSAN | 6.7 | Enterprise |
| Data Center do NSX | 2.4.1 | Segurança |

## <a name="esxi"></a>ESXi

VMware ESXi é instalado em nódos AVS aprovisionados quando cria uma Nuvem Privada AVS. O ESXi fornece o hipervisor para implantar VMs (máquinas virtuais) de carga de trabalho. Os nós fornecem infraestruturas hiperconvergentes (computação e armazenamento) na sua Nuvem Privada AVS. Os nós fazem parte do aglomerado vSphere na Nuvem Privada AVS. Cada nó tem quatro interfaces de redes físicas conectadas à rede Underlay. Duas interfaces de rede física são usadas para criar um **vSphere Distributed Switch (VDS)** no vCenter e duas são usadas para criar um **comutador virtual distribuído (N-VDS) gerenciado pelo NSX**. As interfaces de rede são configuradas no modo ativo-ativo para alta disponibilidade.

Saiba mais em VMware ESXi

## <a name="vcenter-server-appliance"></a>dispositivo vCenter Server

o aparelho de servidor vCenter (VCSA) fornece as funções de autenticação, gestão e orquestração para soluções VMware (AVS). O VCSA com controlador de serviços de plataforma incorporado (PSC) é implantado quando cria a sua Nuvem Privada AVS. O VCSA é implantado no cluster vSphere que é criado quando implementa a sua Nuvem Privada AVS. Cada Nuvem Privada AVS tem o seu próprio VCSA. Expansão de uma Nuvem Privada AVS adiciona os nódosos ao VCSA na Nuvem Privada AVS.

### <a name="vcenter-single-sign-on"></a>logon único do vCenter

O controlador de serviços de plataforma inserido no VCSA está associado a um **domínio de logon único do vCenter**. O nome de domínio é **AVS.local**. É criado um **CloudOwner@AVS.com** de utilizador predefinido para aceder ao vCenter. Você pode adicionar suas fontes de identidade locais/do Azure Active Directory [para o vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>armazenamento vSAN

As Nuvens Privadas AVS são criadas com armazenamento vSAN totalmente configurado, local para o cluster. São necessários no mínimo três nós do mesmo SKU para criar um cluster vSphere com o vSAN datastore. A eliminação de duplicação e a compactação são habilitadas no repositório de armazenamento vSAN por padrão. Dois grupos de discos são criados em cada nó do cluster vSphere. Cada grupo de discos contém um disco de cache e três discos de capacidade.

Uma política de armazenamento padrão do vSAN é criada no cluster vSphere e aplicada ao repositório de armazenamento vSAN. Essa política determina como os objetos de armazenamento de VM são provisionados e alocados no repositório de armazenamento para garantir o nível de serviço necessário. A política de armazenamento define as **falhas a tolerar (FTT)** e o **método de tolerância a falhas**. Você pode criar novas políticas de armazenamento e aplicá-las às VMs. Para manter o SLA, 25% de capacidade suplente deve ser mantida na loja de dados vSAN. 

### <a name="default-vsan-storage-policy"></a>Política de armazenamento padrão do vSAN

A tabela abaixo mostra os parâmetros padrão da política de armazenamento do vSAN.

| Número de nós no cluster vSphere | FTT | Método de tolerância a falhas |
|------------------------------------|-----|--------------------------|
| 3 e 4 nós | 1 | RAID 1 (espelhamento) – cria 2 cópias |
| 5 a 16 nós | 2 | RAID 1 (espelhamento) – cria 3 cópias |

## <a name="nsx-data-center"></a>Data Center do NSX

O NSX Data Center fornece virtualização de rede, micro segmentação e capacidades de segurança de rede na sua Nuvem Privada AVS. Pode configurar todos os serviços suportados pelo NSX Data Center na sua Nuvem Privada AVS através do NSX. Quando cria uma Nuvem Privada AVS, os seguintes componentes NSX são instalados e configurados.

* Gerenciador de NSXT
* Zonas de transporte
* Perfil de uplink do host e do Edge
* Comutador lógico para transporte de borda, Ext1 e ext2
* Pool de IPS para nó de transporte ESXi
* Pool de IPS para o nó de transporte de borda
* Nós de borda
* Regra de antiafinidade do DRS para VMs de controlador e borda
* Roteador da camada 0
* Habilitar o BGP no roteador tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Os anfitriões ESXi são configurados como um cluster para garantir uma alta disponibilidade da Nuvem Privada AVS. Quando cria uma Nuvem Privada AVS, os componentes de gestão da vSphere são implantados no primeiro cluster. Um pool de recursos é criado para componentes de gerenciamento e todas as VMs de gerenciamento são implantadas nesse pool de recursos. O primeiro cluster não pode ser apagado para encolher a Nuvem Privada AVS. o cluster vSphere fornece alta disponibilidade para VMs usando o **VSPHERE ha**. As falhas a serem toleradas são baseadas no número de nós disponíveis no cluster. Você pode usar a fórmula ```Number of nodes = 2N+1``` em que ```N``` é o número de falhas a tolerar.

### <a name="vsphere-cluster-limits"></a>limites de cluster vSphere

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada AVS (primeiro aglomerado vSphere) | 3 |
| Número máximo de nós em um aglomerado de vSphere em uma Nuvem Privada AVS | 16 |
| Número máximo de nós numa Nuvem Privada AVS | 64 |
| Número máximo de aglomerados vSphere numa Nuvem Privada AVS | 21 |
| Número mínimo de nós em um novo cluster vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infra-estrutura do VMware. Atualmente, esses intervalos podem ocorrer a cada 1-2 meses, mas a frequência é esperada para recusar ao longo do tempo. Este tipo de manutenção pode geralmente ser feito sem interromper o consumo normal dos serviços AVS. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam a funcionar sem nenhum impacto:

* Plano e aplicativos de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e upgrades

A AVS é responsável pela gestão do software VMware (ESXi, vCenter, PSC e NSX) na Nuvem Privada AVS.

As atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs liberadas pelo VMware.
* **Atualizações**. Alteração de versão secundária de um componente de pilha do VMware.
* **Atualizações**. Alteração de versão principal de um componente do VMware Stack.

A AVS testa um patch de segurança crítico assim que fica disponível a partir de VMware. Por SLA, a AVS lança o patch de segurança para ambientes AVS Private Cloud dentro de uma semana.

O AVS fornece atualizações trimestrais de manutenção aos componentes do software VMware. Quando uma nova versão principal do software VMware está disponível, a AVS trabalha com os clientes para coordenar uma janela de manutenção adequada para upgrade. 

## <a name="next-steps"></a>Passos seguintes

* [Manutenção e atualizações avs](cloudsimple-maintenance-updates.md)
