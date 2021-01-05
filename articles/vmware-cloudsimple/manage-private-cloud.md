---
title: Gerir a Solução VMware Azure por CloudSimple Private Cloud
description: Descreve as capacidades disponíveis para gerir os recursos e atividade da CloudSimple Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895211"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerir recursos e atividades da Private Cloud

Nuvens privadas são geridas a partir do portal CloudSimple.  Verifique o estado, os recursos disponíveis, a atividade na nuvem privada e outras configurações a partir do portal CloudSimple.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Ver a Lista de Nuvens Privadas

O separador **Nuvens Privadas** na página **Recursos** lista todas as Nuvens Privadas na sua subscrição. A informação inclui o nome, número de aglomerados vSphere, localização, estado atual da nuvem privada e informações sobre recursos.

![Página de Nuvem Privada](media/manage-private-cloud.png)

Selecione uma Nuvem Privada para obter informações e ações adicionais.

## <a name="private-cloud-summary"></a>Resumo da Nuvem Privada

Veja um resumo completo da Nuvem Privada selecionada.  A página de resumo inclui os servidores DNS implantados na Nuvem Privada.  Pode configurar o encaminhamento de DNS dos servidores DNS no local para os seus servidores DNS da Cloud Privada.  Para obter mais informações sobre o encaminhamento de DNS, consulte [o Configure DNS para resolução de nomes para Private Cloud vCenter a partir do local](./on-premises-dns-setup.md).

![Resumo da nuvem privada](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Lançamento vSphere cliente](./vcenter-access.md). Aceda ao vCenter para esta Nuvem Privada.
* [Comprar nós](create-nodes.md). Adicione nós a esta Nuvem Privada.
* [Expandir.](expand-private-cloud.md) Adicione nós a esta Nuvem Privada.
* **Atualizar**. Atualize as informações nesta página.
* **Eliminar**. Pode apagar a Nuvem Privada a qualquer momento. **Antes de eliminar, certifique-se de que fez o back up de todos os sistemas e dados.** A eliminação de uma Nuvem Privada elimina todos os VMs, configuração vCenter e dados. Clique em **Eliminar** na secção resumo para a Nuvem Privada selecionada. Após a eliminação, todos os dados da Private Cloud são apagados num processo de apagamento seguro e altamente conforme.
* [Alterar privilégios vSphere](escalate-private-cloud-privileges.md).  Aumente os seus privilégios nesta Nuvem Privada.

## <a name="private-cloud-vlanssubnets"></a>Nuvem Privada VLANS/sub-redes

Consulte a lista de VLANs/sub-redes definidas para a Nuvem Privada selecionada.  A lista inclui a gestão VLANs/subnets criadas quando a nuvem privada foi criada.

![Nuvem Privada - VLANs/Subnets](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANS/Subnets](./create-vlan-subnet.md). Adicione um VLAN/subconjunto a esta Nuvem Privada.

Selecione um VLAN/Subnet para seguintes ações
* [Fixe a tabela de firewall](./firewall.md). Fixe uma mesa de firewall a esta Nuvem Privada.
* **Editar**
* **Eliminar** (apenas VLANs/Subnetas definidos pelo utilizador)

## <a name="private-cloud-activity"></a>Atividade da Nuvem Privada

Consulte as seguintes informações para a Nuvem Privada selecionada.  A informação de atividade é uma lista filtrada de todas as atividades para a Nuvem Privada selecionada.  Esta página mostra até 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem Privada - Atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Prateleiras de nuvens

As prateleiras das nuvens são os blocos de construção da sua Nuvem Privada. Cada cremalheira fornece uma unidade de capacidade. O CloudSimple configura automaticamente os racks de nuvem com base nas suas seleções ao criar ou expandir uma Nuvem Privada.  Veja a lista completa de prateleiras de nuvens, incluindo a Nuvem Privada a que cada um está atribuído.

![Nuvem Privada - Prateleiras de Nuvens](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de Gestão do vSphere

Lista de recursos de gestão VMware e máquinas virtuais que estão atualmente configuradas na Nuvem Privada. A informação inclui a versão do software, o nome de domínio totalmente qualificado (FQDN) e o endereço IP dos recursos.

![Nuvem Privada - rede de gestão vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)
