---
title: Componentes VMware em nuvem privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como o serviço CloudSimple permite-lhe implementar vMware de forma nativa em locais Azure. As Nuvens Privadas estão integradas com o resto da Nuvem Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88142507"
---
# <a name="private-cloud-vmware-components"></a>Componentes VMware de nuvem privada

A Private Cloud é um ambiente isolado de pilha de VMware (anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter num domínio de gestão.  O serviço CloudSimple permite-lhe implantar vMware de forma nativa na infraestrutura metálica Azure em locais de Azure.  As Nuvens Privadas estão integradas com o resto da Nuvem Azure.  Uma nuvem privada é implantada com os seguintes componentes de pilha VMware:

* **VMware ESXi -** Hipervisor em Azure nódoa dedicado
* **VMware vCenter -** Aparelho para gestão centralizada do ambiente de nuvem privada vSphere
* **VMware vSAN -** Solução de infraestrutura hiperconverente
* **Centro de Dados VMware NSX -** Software de Virtualização e Segurança de Rede  

## <a name="vmware-component-versions"></a>Versões componentes VMware

Uma pilha de VMware cloud privada é implementada com a seguinte versão de software.

| Componente | Versão | Versão licenciada |
|-----------|---------|------------------|
| ESXi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter Standard |
| vSAN | 6.7 | Grandes Empresas |
| Centro de Dados NSX | 2.4.1 | Avançado |

## <a name="esxi"></a>ESXi

O VMware ESXi está instalado nos nós CloudSimple a provisionados quando cria uma nuvem privada.  O ESXi fornece o hipervisor para a implantação de máquinas virtuais de carga de trabalho (VMs).  Os nós fornecem infraestruturas hiper-convergentes (computação e armazenamento) na sua nuvem privada.  Os nós fazem parte do aglomerado vSphere na nuvem privada.  Cada nó tem quatro interfaces de redes físicas ligadas à rede de base.  Duas interfaces de rede física são usadas para criar um **VSphere Distributed Switch (VDS)** em vCenter e duas são usadas para criar um **interruptor virtual distribuído gerido por NSX (N-VDS)**.  As interfaces de rede estão configuradas em modo ativo para uma elevada disponibilidade.

Saiba mais sobre vMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter servidor

vCenter server appliance (VCSA) fornece as funções de autenticação, gestão e orquestração para VMware Solution by CloudSimple. O VCSA com o Controlador de Serviços de Plataforma incorporado (PSC) é implementado quando cria a sua nuvem privada.  O VCSA é implantado no cluster vSphere que é criado quando implementa a sua nuvem privada.  Cada nuvem privada tem o seu próprio VCSA.  A expansão de uma nuvem privada adiciona os nós ao VCSA na nuvem privada.

### <a name="vcenter-single-sign-on"></a>vCenter single sign-on

O Controlador de Serviços de Plataforma Incorporado no VCSA está associado a um **domínio vCenter Single Sign-On**.  O nome de domínio é **cloudsimple.local.**  É criado um utilizador predefinido **CloudOwner@cloudsimple.com** para aceder ao vCenter.  Pode adicionar as suas fontes de identidade ativas no local/Azure [para o vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>armazenamento vSAN

Nuvens privadas são criadas com armazenamento totalmente configurado all-flash vSAN, local para o cluster.  São necessários três nós mínimos do mesmo SKU para criar um cluster vSphere com a loja de dados vSAN.  A des duplicação e a compressão são ativadas na loja de dados vSAN por padrão.  Dois grupos de discos são criados em cada nó do cluster vSphere. Cada grupo de disco contém um disco de cache e três discos de capacidade.

Uma política de armazenamento vSAN padrão é criada no cluster vSphere e aplicada na loja de dados vSAN.  Esta política determina como os objetos de armazenamento VM são a provisionados e atribuídos dentro da datastore para garantir o nível de serviço exigido.  A política de armazenamento define as **falhas de toleração (FTT)** e o **método de tolerância** à falha .  Pode criar novas políticas de armazenamento e aplicá-las aos VMs. Para manter o SLA, a capacidade disponível de 25% deve ser mantida na loja de dados vSAN.  

### <a name="default-vsan-storage-policy"></a>Política de armazenamento vSAN padrão

A tabela abaixo mostra os parâmetros padrão da política de armazenamento vSAN.

| Número de nós no vSphere Cluster | FTT | Método de tolerância à falha |
|------------------------------------|-----|--------------------------|
| 3 e 4 nódoas | 1 | RAID 1 (espelhamento) - cria 2 cópias |
| 5 a 16 nosmes | 2 | RAID 1 (espelhamento) - cria 3 exemplares |

## <a name="nsx-data-center"></a>Centro de Dados NSX

O NSX Data Center fornece capacidades de virtualização de rede, micro segmentação e segurança de rede na sua nuvem privada.  Pode configurar todos os serviços suportados pelo NSX Data Center na sua nuvem privada através do NSX.  Quando cria uma nuvem privada, os seguintes componentes NSX são instalados e configurados.

* Gerente da NSXT
* Zonas de Transporte
* Perfil de anfitrião e uplink de borda
* Switch lógico para transporte de bordas, Ext1 e Ext2
* Piscina IP para nó de transporte ESXi
* Piscina IP para nó de transporte de borda
* Nó de borda
* Regra anti-afinidade drs para controladores e VMs edge
* Router de nível 0
* Ativar BGP no Tier0 Router

## <a name="vsphere-cluster"></a>vSphere cluster

Os anfitriões ESXi são configurados como um cluster para garantir uma alta disponibilidade da nuvem privada.  Quando se cria uma nuvem privada, os componentes de gestão da vSphere são implantados no primeiro cluster.  É criado um conjunto de recursos para componentes de gestão e todos os VMs de gestão são implantados neste conjunto de recursos. O primeiro aglomerado não pode ser apagado para encolher a nuvem privada.  vSphere cluster fornece alta disponibilidade para VMs usando **vSphere HA**.  As falhas de toleração baseiam-se no número de nós disponíveis no cluster.  Pode usar a fórmula ```Number of nodes = 2N+1``` onde está o número de falhas a ```N``` tolerar.

### <a name="vsphere-cluster-limits"></a>vSphere limites de cluster

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada (primeiro aglomerado vSphere) | 3 |
| Número máximo de nós em um aglomerado vSphere em uma nuvem privada | 16 |
| Número máximo de nós numa nuvem privada | 64 |
| Número máximo de aglomerados vSphere em uma nuvem privada | 21 |
| Número mínimo de nós num novo aglomerado vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura VMware

Ocasionalmente é necessário fazer alterações na configuração da infraestrutura VMware. Atualmente, estes intervalos podem ocorrer a cada 1-2 meses, mas espera-se que a frequência diminua ao longo do tempo. Este tipo de manutenção pode geralmente ser feito sem interromper o consumo normal dos serviços CloudSimple. Durante um intervalo de manutenção VMware, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano de gestão VMware e aplicações
* vCenter acesso
* Todo o networking e armazenamento
* Todo o tráfego Azure

## <a name="updates-and-upgrades"></a>Atualizações e atualizações

A CloudSimple é responsável pela gestão do ciclo de vida do software VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

As atualizações de software incluem:

* **Remendos**. Patches de segurança ou correções de erros lançados pela VMware.
* **Atualizações**. Alteração de versão menor de um componente de pilha VMware.
* **Atualizações**. Alteração de versão principal de um componente de pilha VMware.

O CloudSimple testa um patch de segurança crítico assim que estiver disponível a partir de VMware. Per SLA, CloudSimple lança o patch de segurança para ambientes de nuvem privada dentro de uma semana.

O CloudSimple fornece atualizações trimestrais de manutenção aos componentes de software VMware. Quando uma nova versão principal do software VMware está disponível, a CloudSimple trabalha com os clientes para coordenar uma janela de manutenção adequada para upgrade.  

## <a name="next-steps"></a>Passos seguintes

* [CloudSimple manutenção e atualizações](cloudsimple-maintenance-updates.md)
