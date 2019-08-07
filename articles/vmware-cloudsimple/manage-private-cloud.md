---
title: Gerenciar a solução Azure VMware da nuvem privada CloudSimple
description: Descreve os recursos disponíveis para gerenciar seus recursos e atividades de nuvem privada do CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812265"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerenciar recursos e atividades de nuvem privada

Nuvens privadas são gerenciadas do portal do CloudSimple.  Verifique o status, os recursos disponíveis, a atividade na nuvem privada e outras configurações no portal do CloudSimple.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Exibir a lista de nuvens privadas

A guia **nuvens privadas** na página **recursos** lista todas as nuvens privadas em sua assinatura. As informações incluem o nome, o número de clusters vSphere, o local, o estado atual da nuvem privada e as informações de recursos.

![Página nuvem privada](media/manage-private-cloud.png)

Selecione uma nuvem privada para obter informações e ações adicionais.

## <a name="private-cloud-summary"></a>Resumo da nuvem privada

Exiba um resumo abrangente da nuvem privada selecionada.  A página Resumo inclui os servidores DNS implantados na nuvem privada.  Você pode configurar o encaminhamento de DNS de servidores DNS locais para seus servidores DNS de nuvem privada.  Para obter mais informações sobre o encaminhamento de DNS, consulte [Configurar o DNS para resolução de nomes para o vCenter de nuvem privada local](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Resumo da nuvem privada](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Inicie o cliente do vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Acesse o vCenter para esta nuvem privada.
* [Nós de compra](create-nodes.md). Adicione nós a esta nuvem privada.
* [Expanda](expand-private-cloud.md). Adicione nós a esta nuvem privada.
* **Atualizar**. Atualize as informações nesta página.
* **Excluir**. Você pode excluir a nuvem privada a qualquer momento. **Antes de excluir, certifique-se de ter feito backup de todos os sistemas e dados.** A exclusão de uma nuvem privada exclui todas as VMs, a configuração do vCenter e os dados. Clique em **excluir** na seção Resumo para a nuvem privada selecionada. Após a exclusão, todos os dados de nuvem privada são apagados em um processo de eliminação seguro e altamente compatível.
* [Altere os privilégios de vSphere](escalate-private-cloud-privileges.md).  Escalonar seus privilégios nesta nuvem privada.

## <a name="private-cloud-vlanssubnets"></a>VLANs/sub-redes de nuvem privada

Exiba a lista de VLANs/sub-redes definidas para a nuvem privada selecionada.  A lista inclui as VLANs de gerenciamento/sub-redes criadas quando a nuvem privada foi criada.

![Nuvem privada-VLANs/sub-redes](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANs/sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adicione uma VLAN/subconjunto a esta nuvem privada.

Selecione uma VLAN/sub-rede para as seguintes ações
* [Anexar tabela de firewall](https://docs.azure.cloudsimple.com/firewall/). Anexe uma tabela de firewall a esta nuvem privada.
* **Editar**
* **Excluir** (somente VLANs/sub-redes definidas pelo usuário)

## <a name="private-cloud-activity"></a>Atividade de nuvem privada

Exiba as informações a seguir para a nuvem privada selecionada.  As informações da atividade são uma lista filtrada de todas as atividades da nuvem privada selecionada.  Esta página mostra até 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem privada-atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks de nuvem

Racks de nuvem são os blocos de construção de sua nuvem privada. Cada rack fornece uma unidade de capacidade. O CloudSimple configura automaticamente os racks de nuvem com base em suas seleções ao criar ou expandir uma nuvem privada.  Exiba a lista completa de racks de nuvem, incluindo a nuvem privada à qual cada um está atribuído.

![Nuvem privada-racks de nuvem](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de gerenciamento de vSphere

Lista de recursos de gerenciamento e máquinas virtuais do VMware que estão configurados atualmente na nuvem privada. As informações incluem a versão do software, o FQDN (nome de domínio totalmente qualificado) e o endereço IP dos recursos.

![Nuvem privada – rede de gerenciamento vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Passos Seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)