---
title: Gerir a solução de VMware do Azure pela nuvem privada de CloudSimple
description: Descreve as capacidades disponíveis para gerir os seus recursos da nuvem privada CloudSimple e atividade
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332738"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerir recursos de nuvem privada e atividade

Nuvens privadas são geridas a partir do portal CloudSimple.  Verifique o estado, a recursos disponíveis, a atividade na nuvem privada e outras definições do CloudSimple portal.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Ver a lista de nuvens privadas

O **nuvens privadas** guia a **recursos** página lista todas as nuvens privadas na sua subscrição. Informações incluem o nome, número de vSphere clusters, localização, estado atual das informações privadas na cloud e de recursos.

![Página de nuvem privada](media/manage-private-cloud.png)

Selecione uma nuvem privada para obter mais informações e ações.

## <a name="private-cloud-summary"></a>Resumo de nuvem privada

Ver um resumo abrangente da nuvem privada selecionada.  Página de resumo inclui os servidores DNS implementados na Cloud privada.  Pode configurar a DNS de reencaminhamento de servidores DNS no local para os seus servidores de DNS de nuvem privada.  Para obter mais informações sobre o reencaminhamento de DNS, consulte [configurar o DNS para resolução de nomes para o vCenter de nuvem privada no local](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Resumo da nuvem privada](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Inicie o cliente do vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Aceda ao vCenter para esta nuvem privada.
* [Adquirir nós](create-nodes.md). Adicione nós para esta nuvem privada.
* [Expand](expand-private-cloud.md). Adicione nós para esta nuvem privada.
* **Refresh**. Atualize as informações nesta página.
* **Eliminar**. É possível eliminar a nuvem privada em qualquer altura. **Antes de eliminar, certifique-se de que efetuou uma cópia de todos os sistemas e dados.** A eliminar uma nuvem privada elimina todas as VMs, configuração de vCenter e dados. Clique em **eliminar** na secção de resumo para a nuvem privada selecionada. Após a eliminação, todos os dados de nuvem privada é apagado num processo de eliminação segura e altamente compatível.
* [Alterar os privilégios de vSphere](escalate-private-cloud-privileges.md).  Aumentar os seus privilégios nesta nuvem privada.

## <a name="private-cloud-vlanssubnets"></a>Private Cloud VLANS/sub-redes

Ver a lista de VLANs/sub-redes definidas para a nuvem privada selecionada.  A lista inclui a gestão de VLANs/sub-redes criadas quando a nuvem privada foi criada.

![Nuvem privada - VLANs/sub-redes](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANS/sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adicione um subconjunto/VLAN para esta nuvem privada.

Selecione uma sub-rede/VLAN para as ações seguintes
* [Anexar a tabela de firewall](https://docs.azure.cloudsimple.com/firewall/). Anexe uma tabela de firewall para esta nuvem privada.
* **Editar**
* **Eliminar** (apenas definidas pelo utilizador VLANs/sub-redes)

## <a name="private-cloud-activity"></a>Atividade de nuvem privada

Ver as seguintes informações para a nuvem privada selecionada.  As informações de atividade são uma lista filtrada de todas as atividades para a nuvem privada selecionada.  Esta página apresenta até 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem privada - atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks de cloud

Racks de cloud são os blocos modulares de sua nuvem privada. Cada bastidor fornece uma unidade de capacidade. CloudSimple configura automaticamente racks de cloud com base nas suas seleções quando criar ou expandir uma nuvem privada.  Ver a lista completa de racks de cloud, incluindo a nuvem privada que cada é atribuída a.

![Nuvem privada - Racks de Cloud](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de gestão do vSphere

Lista de recursos de gerenciamento do VMware e máquinas virtuais que estão atualmente configuradas na Cloud privada. Informações incluem a versão do software, o nome de domínio completamente qualificado (FQDN) e o endereço IP dos recursos.

![Nuvem privada - vSphere rede de gestão](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passos Seguintes

* [Consumir as VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)