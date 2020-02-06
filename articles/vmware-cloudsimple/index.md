---
title: Azure VMware Solutions (AVS)
description: Portal da documentação do Azure VMware Solutions (AVS).
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d81ea6778f3ba31d72c34334b1439994b076647c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025219"
---
# <a name="azure-vmware-solution-by-avs"></a>Azure VMware Solution by AVS

Bem-vindo ao portal único onde pode obter ajuda para o Azure VMware Solution by AVS.
No site da documentação, pode saber mais sobre os seguintes tópicos:

## <a name="overview"></a>Descrição geral

Saiba mais sobre o Azure VMware Solutions (AVS)

* Saiba mais sobre os benefícios, funcionalidades e cenários de utilização em [O que é o Azure VMware Solution by AVS](cloudsimple-vmware-solutions-overview.md)
* Consulte os [principais conceitos para administração](key-concepts.md)

## <a name="quickstart"></a>Início Rápido

Saiba como começar a utilizar a solução

* Compreenda como [inicializar o serviço e comprar capacidade](quickstart-create-cloudsimple-service.md)
* Saiba como criar um novo ambiente VMware em [Configurar um Ambiente de Cloud Privada do AVS](quickstart-create-private-cloud.md)
* Saiba como unificar a gestão no VMware e no Azure no artigo [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Conceitos

Saiba mais sobre os seguintes conceitos

* [Serviço AVS](cloudsimple-service.md) (também conhecido como "Azure VMware Solutions (AVS) - Serviço"). Este recurso tem de ser criado uma vez por região.
* Compre capacidade para o seu ambiente ao criar um ou mais recursos do [Nó do AVS](cloudsimple-node.md). Estes recursos também são conhecidos como "Azure VMware Solution by AVS - Nó".
* Inicialize e configure o seu ambiente VMware com as [Clouds Privadas do AVS](cloudsimple-private-cloud.md).
* Unifique a gestão com as [Máquinas Virtuais do AVS](cloudsimple-virtual-machines.md) (também conhecidas como "Azure VMware Solutions (AVS) - Máquina virtual").
* Crie a rede subjacente com [VLANs/sub-redes](cloudsimple-vlans-subnets.md).
* Segmente e proteja a sua rede subjacente com o recurso [Tabela de Firewall](cloudsimple-firewall-tables.md).
* Obtenha acesso seguro aos seus ambientes VMware através da WAN com [Gateways de VPN](cloudsimple-vpn-gateways.md).
* Permita o acesso público para as cargas de trabalho com o [IP Público](cloudsimple-public-ip-address.md).
* Estabeleça a conectividade às Redes Virtuais do Azure e às redes no local através da [Ligação de Rede do Azure](cloudsimple-azure-network-connection.md).
* Configure destinos de email de alerta com a [Gestão de Contas](cloudsimple-account.md).
* Veja registos das atividades do utilizador e do sistema com os ecrãs [Gestão de Atividades](cloudsimple-activity.md).
* Compreenda os vários [componentes do VMware ](vmware-components.md).

## <a name="tutorials"></a>Tutoriais

Saiba como realizar tarefas comuns, tais como:

* [Criar um Serviço AVS](create-cloudsimple-service.md) uma vez por região, na qual quer implementar ambientes VMware.
* Gerir a funcionalidade de serviço principal no [portal do AVS](access-cloudsimple-portal.md).
* Ativar a capacidade e otimizar a faturação para a sua infraestrutura ao [Comprar nós do AVS](create-nodes.md).
* Gerir configurações de ambiente VMware com Clouds Privadas do AVS. Pode [criar](create-private-cloud.md), [gerir](manage-private-cloud.md), [expandir](expand-private-cloud.md) ou [encolher](shrink-private-cloud.md) Clouds Privadas do AVS.
* Ativar a gestão unificada ao [mapear Subscrições do Azure](azure-subscription-mapping.md).
* Monitorizar as atividades do utilizador e do sistema com as [Páginas de Atividades](monitor-activity.md).
* Configurar a rede para os seus ambientes ao [criar e gerir sub-redes](create-vlan-subnet.md).
* Segmentar e proteger o seu ambiente com [Tabelas e regras de firewall](firewall.md).
* Permitir o acesso da Internet de entrada para cargas de trabalho ao [Alocar IPs Públicos](public-ips.md).
* Permitir a conectividade a partir das suas redes internas ou estações de trabalho de cliente ao [configurar uma VPN](vpn-gateway.md).
* Permitir as comunicações a partir dos seus [ambientes no local](on-premises-connection.md) e, também, para as [Redes virtuais do Azure](virtual-network-connection.md).
* Configurar destinos de alerta e ver a capacidade total adquirida no [resumo da conta](account.md)
* Ver os [utilizadores](users.md) que acederam ao portal do AVS.
* Gerir máquinas virtuais do VMware no portal do Azure:
    * [Crie máquinas virtuais](azure-create-vm.md) no portal do Azure.
    * [Gira as máquinas virtuais](azure-manage-vm.md) criadas por si.

## <a name="how-to-guides"></a>Guias de Procedimentos

Estes guias descrevem as soluções para atingir metas como:

* [Proteger o seu ambiente](private-cloud-secure.md)
* Instalar ferramentas de terceiros, permitir utilizadores adicionais e uma origem de autenticação externa no vSphere através do [escalamento de privilégios](escalate-privileges.md).
* Configurar o acesso a vários serviços do VMware ao [configurar um DNS no local](on-premises-dns-setup.md).
* Ativar a alocação de nomes e endereços para as suas cargas de trabalho ao [configurar o DNS e o DHCP da carga de trabalho](dns-dhcp-setup.md).
* Compreender como o serviço garante a segurança e funcionalidade na sua plataforma através de [atualizações](vmware-components.md#updates-and-upgrades) de serviço.
* Guardar o TCO como cópia de segurança ao criar uma arquitetura de cópia de segurança de exemplo com um [software de cópia de segurança de terceiros como o Veeam](backup-workloads-veeam.md).
* Criar um ambiente seguro ao ativar a encriptação inativa com um [software de encriptação de KMS de terceiros](vsan-encryption.md).
* Expandir a gestão do Azure Active Directory (Azure AD) para o VMware ao configurar a [origem de identidade do Azure AD](azure-ad.md).
