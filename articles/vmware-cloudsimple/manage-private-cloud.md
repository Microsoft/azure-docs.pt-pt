---
title: Gerir soluções Azure VMware (AVS) Private Cloud
description: Descreve as capacidades disponíveis para gerir os seus recursos e atividade seletivas da Nuvem Privada AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014832"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Gerir recursos e atividades privadas da AVS Cloud

As nuvens privadas aVS são geridas a partir do portal AVS. Verifique o estado, os recursos disponíveis, a atividade na nuvem privada AVS e outras configurações do portal AVS.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Aceda ao portal AVS

Aceda ao [portal AVS.](access-cloudsimple-portal.md)

## <a name="view-the-list-of-avs-private-clouds"></a>Ver a Lista de Nuvens Privadas AVS

O separador **AVS Private Clouds** na página **Recursos** lista todas as Nuvens Privadas AVS na sua subscrição. A informação inclui o nome, número de clusters vSphere, localização, estado atual da Nuvem Privada AVS e informações de recursos.

![Página de Nuvem Privada AVS](media/manage-private-cloud.png)

Selecione uma Nuvem Privada AVS para obter informações e ações adicionais.

## <a name="avs-private-cloud-summary"></a>Resumo da Nuvem Privada AVS

Veja um resumo abrangente da Nuvem Privada AVS selecionada. A página sumária inclui os servidores DNS implantados na Nuvem Privada AVS. Pode configurar o reencaminhado dNS dos servidores DNS no local para os seus servidores DNS Privados de Nuvem AVS. Para obter mais informações sobre o encaminhamento do DNS, consulte [configure DNS para resolução de nome si para AVS Private Cloud vCenter a partir do local](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Resumo privado da nuvem da AVS](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Lançar cliente vSphere.](https://docs.azure.cloudsimple.com/vsphere-access/) Aceda ao vCenter para esta Nuvem Privada AVS.
* [Comprar os nódosos.](create-nodes.md) Adicione nós a esta Nuvem Privada AVS.
* [Expandir](expand-private-cloud.md). Adicione nós a esta Nuvem Privada AVS.
* **Refrescar.** Atualize a informação nesta página.
* **Apagar**. Pode eliminar a Nuvem Privada AVS a qualquer momento. **Antes de apagar, certifique-se de que já fez o apoio a todos os sistemas e dados.** Eliminar uma Nuvem Privada AVS elimina todos os VMs, configuração vCenter e dados. Clique em **Apagar** na secção resumo para a Nuvem Privada AVS selecionada. Após a eliminação, todos os dados da AVS Private Cloud são apagados num processo de apagamento seguro e altamente compatível.
* [Alterar privilégios vSphere.](escalate-private-cloud-privileges.md) Aumente os seus privilégios nesta Nuvem Privada AVS.

## <a name="avs-private-cloud-vlanssubnets"></a>Nuvem Privada VLANS/subnets da AVS

Consulte a lista de VLANs/subredes definidas para a Nuvem Privada AVS selecionada. A lista inclui a gestão VLANs/subnets criadas quando a Nuvem Privada AVS foi criada.

![Nuvem Privada AVS - VLANs/Subnets](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicione VLANS/Subnets](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adicione um VLAN/subconjunto a esta Nuvem Privada AVS.

Selecione um VLAN/Subnet para as seguintes ações
* [Fixe a tabela de firewall](https://docs.azure.cloudsimple.com/firewall/). Fixe uma tabela de firewall a esta Nuvem Privada AVS.
* **Editar**
* **Eliminar** (apenas VLANs/Subnets definidos pelo utilizador)

## <a name="avs-private-cloud-activity"></a>Atividade da Nuvem Privada AVS

Consulte as seguintes informações para a Nuvem Privada AVS selecionada. A informação de atividade é uma lista filtrada de todas as atividades para a Nuvem Privada AVS selecionada. Esta página mostra 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem Privada AVS - Atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Prateleiras de nuvens

As prateleiras de nuvens são os blocos de construção da sua Nuvem Privada AVS. Cada cremalheira fornece uma unidade de capacidade. O AVS configura automaticamente as prateleiras de nuvem com base nas suas seleções ao criar ou expandir uma Nuvem Privada AVS. Veja a lista completa de prateleiras de nuvem, incluindo a Nuvem Privada AVS a que cada um é atribuído.

![Nuvem Privada AVS - Cloud Racks](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>rede de gestão vSphere

Lista de recursos de gestão vMware e máquinas virtuais que estão atualmente configuradas na Nuvem Privada AVS. As informações incluem a versão do software, o nome de domínio totalmente qualificado (FQDN) e o endereço IP dos recursos.

![AVS Private Cloud - vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [as Nuvens Privadas aVS](cloudsimple-private-cloud.md)