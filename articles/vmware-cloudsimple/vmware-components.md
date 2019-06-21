---
title: Solução de VMware do Azure por CloudSimple - componentes de VMware de nuvem privada
description: Descreve como os componentes de VMware são instalados em nuvem privada
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 62511118edb4f8b5061f90138bac2aa2b5d3cfe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165159"
---
# <a name="private-cloud-vmware-components"></a>Componentes de VMware de nuvem privada

Uma nuvem privada é uma pilha de VMware isolada (anfitriões ESXi, vCenter, vSAN e NSX) gerido por um vCenter server num domínio de gestão do ambiente.  O serviço de CloudSimple permite-lhe implementar VMware nativamente na infraestrutura do Azure de metal bare em localizações do Azure.  Nuvens privadas estão integradas com o restante da Cloud do Azure.  Uma nuvem privada é implementada com os seguintes componentes de pilha do VMware:

* **VMware ESXi -** nós dedicados de hipervisor no Azure
* **VMware vCenter -** dispositivo para a gestão centralizada de ambiente de vSphere de nuvem privada
* **VSAN da VMware -** solução de infraestrutura hiperconvergida
* **Centro de dados do VMware NSX -** Virtualização e o Software de segurança de rede  

## <a name="vmware-component-versions"></a>Versões de componente de VMware

Uma pilha de VMware de nuvem privada é implementada com a seguinte versão de software.

| Componente | Version | Versão licenciada |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Empresarial |
| NSX Data Center | 2.3 | Avançado |

## <a name="esxi"></a>ESXi

VMware ESXi está instalado em nós de CloudSimple aprovisionados quando criar uma nuvem privada.  ESXi fornece o hipervisor para implementar máquinas virtuais de carga de trabalho (VMs).  Nós fornecem infraestrutura hiperconvergida (computação e armazenamento) na sua nuvem privada.  Os nós são uma parte do cluster vSphere na cloud privada.  Cada nó tem quatro interfaces de redes físicas ligadas à rede de underlay.  Duas interfaces de rede físico são utilizados para criar uma **mudar distribuída vSphere (VDS)** no vCenter e duas são utilizados para criar um **NSX gerido distribuído comutador virtual (N VDS)** .  Interfaces de rede são configuradas no modo ativo-ativo para elevada disponibilidade.

Saiba mais sobre o VMware ESXi

## <a name="vcenter-server-appliance"></a>aplicação de servidor do vCenter

aplicação de servidor vCenter (VCSA) fornece as funções de autenticação, gestão e orquestração para solução de VMware ao CloudSimple. VCSA com o controlador de serviços de plataforma embedded (PSC) é implementado quando criar sua nuvem privada.  VCSA é implementada no cluster vSphere que foi criado quando implementou a sua nuvem privada.  Cada nuvem privada tem seu próprio VCSA.  Expansão de uma nuvem privada adiciona nós VCSA na cloud privada.

### <a name="vcenter-single-sign-on"></a>vCenter início de sessão único

Controlador de serviços de plataforma incorporado no VCSA está associado um **vCenter. o início de sessão único domínio**.  O nome de domínio é **cloudsimple.local**.  Um utilizador padrão **CloudOwner@cloudsimple.com** é criada para que possa aceder ao vCenter.  Pode adicionar no local/Azure active directory [origens de identidade do vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>armazenamento de vSAN

Nuvens privadas são criadas com o armazenamento de totalmente configurado tudo flash vSAN, local para o cluster.  Número mínimo de três nós do mesmo SKU é necessários para criar um cluster de vSphere com arquivo de dados de vSAN.  A eliminação de duplicados e compressão estão ativadas por predefinição no arquivo de dados vSAN.  Dois grupos de disco são criados em cada nó do vSphere cluster. Cada grupo de disco contém um disco de cache e de três discos de capacidade.

Uma política de armazenamento predefinida vSAN é criada no vSphere cluster e aplicada para o arquivo de dados de vSAN.  Esta política determina como os objetos de armazenamento VM são aprovisionados e alocados no arquivo de dados para garantir o nível de serviço necessário.  A política de armazenamento define a **falhas a tolerar (FTT)** e o **método de tolerância a falhas**.  Pode criar novas políticas de armazenamento e aplicá-las para as VMs. Para manter o SLA, a capacidade de reserva de 25% deve ser mantida no arquivo de dados vSAN.  

### <a name="default-vsan-storage-policy"></a>Política de armazenamento predefinida vSAN

Tabela a seguir mostra as predefinições de parâmetros de política de armazenamento de vSAN.

| Número de nós no Cluster do vSphere | FTT | Método de tolerância a falhas |
|------------------------------------|-----|--------------------------|
| 3 e 4 nós | 1 | RAID 1 (espelhamento) - cria 2 cópias |
| nós de 5 a 16 | 2 | RAID 1 (espelhamento) - cria 3 cópias |

## <a name="nsx-data-center"></a>NSX Data Center

Centro de dados de NSX fornece Virtualização de rede, segmentação micro e capacidades de segurança de rede na sua nuvem privada.  Pode configurar todos os serviços suportados pelo centro de dados de NSX na sua nuvem privada através de NSX.  Quando criar uma nuvem privada, os seguintes componentes NSX instalados e configurados.

* NSXT Manager
* Zonas de transporte
* Anfitrião e o perfil de Uplink do Edge
* Comutador lógico para o transporte de borda, Ext1 e Ext2
* Conjunto IP para o nó de transporte de ESXi
* Conjunto IP para o nó de transporte de borda
* Nós de extremidade
* Regra de antiafinidade DRS para o controlador e as VMs do Edge
* Router de camada 0
* Ativar o BGP no Tier0 Router

## <a name="vsphere-cluster"></a>vSphere cluster

Anfitriões ESXi estão configurados como um cluster para garantir a elevada disponibilidade da nuvem privada.  Quando criar uma nuvem privada, os componentes de gerenciamento do vSphere são implementadas no cluster primeiro.  Um agrupamento de recursos é criado para componentes de gerenciamento e todas as VMs de gestão são implementadas neste conjunto de recursos. Não é possível eliminar o cluster de primeira para reduzir a nuvem privada.  vSphere cluster fornece elevada disponibilidade para VMs com **vSphere HA**.  Falhas a tolerar baseiam-se no número de nós disponíveis no cluster.  Pode usar a fórmula ```Number of nodes = 2N+1``` onde ```N``` é o número de falhas a tolerar.

### <a name="vsphere-cluster-limits"></a>limites de cluster vSphere

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada (primeiro cluster do vSphere) | 3 |
| Número máximo de nós num do vSphere de Cluster numa nuvem privada | 16 |
| Número máximo de nós numa nuvem privada | 64 |
| Número máximo de vSphere Clusters numa nuvem privada | 21 |
| Número mínimo de nós a um novo Cluster do vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção de infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infraestrutura do VMware. Atualmente, estes intervalos podem ocorrer a todos os meses de 1 a 2, mas espera-se a frequência para recusar ao longo do tempo. Este tipo de manutenção normalmente pode ser feito sem interromper o consumo dos serviços CloudSimple normal. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam a funcionar sem qualquer impacto:

* Plano de gestão do VMware e de aplicações
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e melhoramentos

CloudSimple é responsável pelo gerenciamento de ciclo de vida do software de VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

Atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de erros, lançadas pela VMware.
* **Atualizações**. Alteração de versão secundária de um componente da pilha de VMware.
* **Atualizações**. Alteração de versão principal de um componente da pilha de VMware.

CloudSimple testa um patch de segurança críticas, assim que ele se torna disponível a partir do VMware. Por SLA, CloudSimple lança o patch de segurança para ambientes de nuvem privada numa semana.

CloudSimple fornece atualizações de manutenção trimestral aos componentes de software do VMware. Quando uma nova versão principal do software de VMware estiver disponível, CloudSimple funciona com os clientes para coordenar uma janela de manutenção adequada para a atualização.  

## <a name="next-steps"></a>Passos Seguintes

* [CloudSimple manutenção e atualizações](cloudsimple-maintenance-updates.md)