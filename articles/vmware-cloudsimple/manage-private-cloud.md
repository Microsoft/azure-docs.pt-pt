---
title: Gerir a solução Azure VMware pela CloudSimple Private Cloud
description: Descreve as capacidades disponíveis para gerir os seus recursos e atividade seleções cloudsimple private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869292"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerir recursos e atividades privadas cloud

As nuvens privadas são geridas a partir do portal CloudSimple.  Verifique o estado, os recursos disponíveis, a atividade na nuvem privada e outras configurações do portal CloudSimple.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Ver a Lista de Nuvens Privadas

O separador **Private Clouds** na página **Recursos** lista todas as Nuvens Privadas na sua subscrição. A informação inclui o nome, número de aglomerados vSphere, localização, estado atual da nuvem privada e informações de recursos.

![Página de Nuvem Privada](media/manage-private-cloud.png)

Selecione uma Nuvem Privada para obter informações e ações adicionais.

## <a name="private-cloud-summary"></a>Resumo da Nuvem Privada

Veja um resumo abrangente da nuvem privada selecionada.  A página sumária inclui os servidores DNS implantados na Cloud Privada.  Pode configurar o reencaminhado dNS dos servidores DNS no local para os seus servidores DNS de Cloud Privado.  Para obter mais informações sobre o encaminhamento do DNS, consulte [Configure DNS para resolução de nome saque para private Cloud vCenter a partir do local](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/).

![Resumo privado da nuvem](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Lançar cliente vSphere.](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access) Aceda ao vCenter para esta Nuvem Privada.
* [Comprar os nódosos.](create-nodes.md) Adicione nós a esta Nuvem Privada.
* [Expandir](expand-private-cloud.md). Adicione nós a esta Nuvem Privada.
* **Refrescar.** Atualize a informação nesta página.
* **Apagar**. Pode eliminar a Nuvem Privada a qualquer momento. **Antes de apagar, certifique-se de que já fez o apoio a todos os sistemas e dados.** Eliminar uma Nuvem Privada elimina todos os VMs, configuração vCenter e dados. Clique em **Apagar** na secção resumo para a nuvem privada selecionada. Após a eliminação, todos os dados da Cloud Privada são apagados num processo de apagamento seguro e altamente compatível.
* [Alterar privilégios vSphere.](escalate-private-cloud-privileges.md)  Aumente os seus privilégios nesta Nuvem Privada.

## <a name="private-cloud-vlanssubnets"></a>Nuvem Privada VLANS/subnets

Consulte a lista de VLANs/subnets definidos para a nuvem privada selecionada.  A lista inclui a gestão VLANs/subnets criadas quando a nuvem privada foi criada.

![Nuvem Privada - VLANs/Subnets](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicione VLANS/Subnets](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/). Adicione um VLAN/subconjunto a esta Nuvem Privada.

Selecione um VLAN/Subnet para as seguintes ações
* [Fixe a tabela de firewall](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/). Prenda uma mesa de firewall a esta nuvem privada.
* **Editar**
* **Eliminar** (apenas VLANs/Subnets definidos pelo utilizador)

## <a name="private-cloud-activity"></a>Atividade privada da Nuvem

Consulte as seguintes informações para a Nuvem Privada selecionada.  A informação de atividade é uma lista filtrada de todas as atividades para a Nuvem Privada selecionada.  Esta página mostra 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem Privada - Atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Prateleiras de nuvens

As prateleiras das nuvens são os blocos de construção da sua Nuvem Privada. Cada cremalheira fornece uma unidade de capacidade. A CloudSimple confunde automaticamente as prateleiras de nuvem com base nas suas seleções ao criar ou expandir uma Nuvem Privada.  Veja a lista completa de prateleiras de nuvens, incluindo a Nuvem Privada a que cada um é atribuído.

![Nuvem Privada - Cloud Racks](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de Gestão do vSphere

Lista de recursos de gestão vMware e máquinas virtuais que estão atualmente configuradas na Nuvem Privada. As informações incluem a versão do software, o nome de domínio totalmente qualificado (FQDN) e o endereço IP dos recursos.

![Private Cloud - rede de gestão vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)