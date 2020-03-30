---
title: Componentes VMware de nuvem privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como os componentes VMware são instalados na nuvem privada
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279510"
---
# <a name="private-cloud-vmware-components"></a>Componentes VMware de nuvem privada

Uma Nuvem Privada é uma pilha de VMware isolada (anfitriões ESXi, vCenter, vSAN e NSX) gerida por um servidor vCenter em um domínio de gestão.  O serviço CloudSimple permite-lhe implementar vMware de forma nativa em infraestruturas metálicas azure em localizações Azure.  As Nuvens Privadas estão integradas com o resto da Nuvem Azure.  Uma nuvem privada é implantada com os seguintes componentes de pilha VMware:

* **VMware ESXi -** Hipervisor em Azure dedicou nódosos
* **VMware vCenter -** Aparelho para gestão centralizada do ambiente privado nuvem vSphere
* **VMware vSAN -** Solução de infraestrutura hiperconvergente
* **VMware NSX Data Center -** Virtualização da rede e software de segurança  

## <a name="vmware-component-versions"></a>Versões componentes VMware

Uma pilha privada de VMware cloud é implementada com a seguinte versão de software.

| Componente | Versão | Versão licenciada |
|-----------|---------|------------------|
| ESXi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| Centro de Dados NSX | 2.4.1 | Avançado |

## <a name="esxi"></a>ESXi

VMware ESXi está instalado em nódos CloudSimple aprovisionados quando cria uma nuvem privada.  O ESXi fornece o hipervisor para a implementação de máquinas virtuais de carga de trabalho (VMs).  Os nós fornecem infraestruturas hiperconvergentes (computação e armazenamento) na sua nuvem privada.  Os nós fazem parte do aglomerado de vSphere na nuvem privada.  Cada nó tem quatro interfaces de redes físicas ligadas à rede de subposição.  Duas interfaces de rede física são usadas para criar um **VSphere Distributed Switch (VDS)** no vCenter e duas são usadas para criar um interruptor virtual distribuído virtual **gerido pelo NSX (N-VDS)**.  As interfaces de rede estão configuradas em modo ativo para alta disponibilidade.

Saiba mais sobre VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter servidor

o aparelho de servidor vCenter (VCSA) fornece as funções de autenticação, gestão e orquestração para a Solução VMware pela CloudSimple. O VCSA com controlador de serviços de plataforma incorporado (PSC) é implantado quando cria a sua nuvem privada.  O VCSA é implantado no cluster vSphere que é criado quando implanta a sua nuvem privada.  Cada nuvem privada tem o seu próprio VCSA.  A expansão de uma nuvem privada adiciona os nódosos ao VCSA na nuvem privada.

### <a name="vcenter-single-sign-on"></a>vCenter único sign-on

O controlador de serviços de plataforma incorporado no VCSA está associado a um **domínio de sinal único vCenter**.  O nome de domínio é **cloudsimple.local**.  É criado **CloudOwner@cloudsimple.com** um utilizador predefinido para aceder ao vCenter.  Pode adicionar as suas fontes de identidade de diretório ativo no local/Azure [para vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>armazenamento vSAN

As nuvens privadas são criadas com armazenamento vSAN totalmente configurado, local para o cluster.  São necessários três nós mínimos do mesmo SKU para criar um cluster vSphere com uma loja de dados vSAN.  A desduplicação e a compressão estão ativadas na loja de dados vSAN por padrão.  Dois grupos de discos são criados em cada nó do cluster vSphere. Cada grupo de discos contém um disco de cache e três discos de capacidade.

Uma política de armazenamento vSAN predefinida é criada no cluster vSphere e aplicada na loja de dados vSAN.  Esta política determina como os objetos de armazenamento VM são aprovisionados e atribuídos dentro do datastore para garantir o nível de serviço exigido.  A política de armazenamento define as **falhas de tolerar (FTT)** e o método de tolerância à **falha.**  Pode criar novas políticas de armazenamento e aplicá-las aos VMs. Para manter o SLA, 25% de capacidade suplente deve ser mantida na loja de dados vSAN.  

### <a name="default-vsan-storage-policy"></a>Política de armazenamento vSAN predefinido

A tabela abaixo mostra os parâmetros padrão da política de armazenamento vSAN.

| Número de nós no Aglomerado vSphere | FTT | Método de tolerância à insucesso |
|------------------------------------|-----|--------------------------|
| 3 e 4 nós | 1 | RAID 1 (espelhamento) - cria 2 cópias |
| 5 a 16 nódosos | 2 | RAID 1 (espelhamento) - cria 3 cópias |

## <a name="nsx-data-center"></a>Centro de Dados NSX

O NSX Data Center fornece virtualização da rede, micro segmentação e capacidades de segurança de rede na sua nuvem privada.  Pode configurar todos os serviços suportados pelo NSX Data Center na sua nuvem privada através do NSX.  Quando cria uma nuvem privada, os seguintes componentes NSX são instalados e configurados.

* Gestor NSXT
* Zonas de Transporte
* Perfil de anfitrião e edge uplink
* Switch lógico para transporte de borda, Ext1 e Ext2
* Piscina IP para nó de transporte ESXi
* Piscina IP para nó de transporte de borda
* Nódeos de borda
* Regra de anti-afinidade DRS para controlador e VMs de borda
* Router nível 0
* Ativar bGP no Router Tier0

## <a name="vsphere-cluster"></a>aglomerado vSphere

Os anfitriões ESXi são configurados como um cluster para garantir uma alta disponibilidade da nuvem privada.  Quando se cria uma nuvem privada, os componentes de gestão da vSphere são implantados no primeiro cluster.  Um conjunto de recursos é criado para componentes de gestão e todos os VMs de gestão são implantados neste conjunto de recursos. O primeiro aglomerado não pode ser apagado para encolher a nuvem privada.  o cluster vSphere proporciona uma elevada disponibilidade para VMs utilizando **vSphere HA**.  As falhas na tolerar baseiam-se no número de nós disponíveis no cluster.  Pode usar a ```Number of nodes = 2N+1``` ```N``` fórmula onde está o número de falhas a tolerar.

### <a name="vsphere-cluster-limits"></a>limites de cluster vSphere

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada (primeiro aglomerado vSphere) | 3 |
| Número máximo de nós em um aglomerado de vSphere em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número máximo de aglomerados de vSphere numa nuvem privada | 21 |
| Número mínimo de nós num novo aglomerado de vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura VMware

Ocasionalmente é necessário fazer alterações na configuração da infraestrutura VMware. Atualmente, estes intervalos podem ocorrer a cada 1-2 meses, mas espera-se que a frequência diminua ao longo do tempo. Este tipo de manutenção pode geralmente ser feito sem interromper o consumo normal dos serviços CloudSimple. Durante um intervalo de manutenção VMware, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano e aplicações de gestão VMware
* acesso vCenter
* Todo o networking e armazenamento
* Todo o tráfego azure

## <a name="updates-and-upgrades"></a>Atualizações e atualizações

A CloudSimple é responsável pela gestão do software VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

As atualizações de software incluem:

* **Remendos.** Patches de segurança ou correções de bugs lançadas pela VMware.
* **Atualizações**. Alteração de versão menor de um componente de pilha VMware.
* **Upgrades**. Mudança de versão importante de um componente de pilha VMware.

A CloudSimple testa um patch de segurança crítico assim que fica disponível a partir de VMware. Por SLA, a CloudSimple lança o patch de segurança para ambientes privados de nuvem dentro de uma semana.

A CloudSimple fornece atualizações trimestrais de manutenção aos componentes do software VMware. Quando uma nova versão principal do software VMware está disponível, a CloudSimple trabalha com os clientes para coordenar uma janela de manutenção adequada para upgrade.  

## <a name="next-steps"></a>Passos seguintes

* [Manutenção e atualizações CloudSimple](cloudsimple-maintenance-updates.md)
