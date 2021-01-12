---
title: Azure VMware Solution by CloudSimple
description: Saiba mais sobre o Azure VMware Solution by CloudSimple, incluindo uma descrição geral, inícios rápidos, conceitos, tutoriais e manuais de instruções.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: vms support, azure vmware solution by cloudsimple, cloudsimple azure, vms tools, vmware documentation
ms.openlocfilehash: a3514a011f940b82702984737d0a93c5da402b55
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108386"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Bem-vindo ao portal único onde pode obter ajuda para a Azure VMware Solution by CloudSimple.
No site da documentação, pode saber mais sobre os seguintes tópicos:

## <a name="overview"></a>Descrição geral

Saiba mais sobre a Azure VMware Solution by CloudSimple

* Saiba mais sobre os benefícios, funcionalidades e cenários de utilização em [O que é a Azure VMware Solution by CloudSimple](cloudsimple-vmware-solutions-overview.md)
* Consulte os [principais conceitos para administração](key-concepts.md)

## <a name="quickstart"></a>Início Rápido

Saiba como começar a utilizar a solução

* Compreenda como [inicializar o serviço e comprar capacidade](quickstart-create-cloudsimple-service.md)
* Saiba como criar um novo ambiente VMware em [Configurar um Ambiente de Cloud Privada](quickstart-create-private-cloud.md)
* Saiba como unificar a gestão no VMware e no Azure no artigo [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Conceitos

Saiba mais sobre os seguintes conceitos

* Um [Serviço da CloudSimple](cloudsimple-service.md) (também conhecido como "Azure VMware Solution by CloudSimple - Serviço"). Este recurso tem de ser criado uma vez por região.
* Compre capacidade para o seu ambiente ao criar um ou mais recursos do [Nó da CloudSimple](cloudsimple-node.md). Estes recursos também são conhecidos como "Azure VMware Solution by CloudSimple - Nó".
* Inicialize e configure o seu ambiente VMware com as [Cloud Privadas](cloudsimple-private-cloud.md).
* Unifique a gestão com as [Máquinas Virtuais da CloudSimple](cloudsimple-virtual-machines.md) (também conhecidas como "Azure VMware Solution by CloudSimple - Máquina virtual").
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

* [Criar um Serviço da CloudSimple](create-cloudsimple-service.md) uma vez por região, na qual quer implementar ambientes VMware.
* Gerir a funcionalidade de serviço principal no [portal da CloudSimple](access-cloudsimple-portal.md).
* Ativar a capacidade e otimizar a faturação para a sua infraestrutura ao [Comprar nós da CloudSimple](create-nodes.md).
* Gerir configurações de ambiente VMware com Clouds Privadas. Pode [criar](create-private-cloud.md), [gerir](manage-private-cloud.md), [expandir](expand-private-cloud.md) ou [encolher](shrink-private-cloud.md) Clouds Privadas.
* Ativar a gestão unificada ao [mapear Subscrições do Azure](azure-subscription-mapping.md).
* Monitorizar as atividades do utilizador e do sistema com as [Páginas de Atividades](monitor-activity.md).
* Configurar a rede para os seus ambientes ao [criar e gerir sub-redes](create-vlan-subnet.md).
* Segmentar e proteger o seu ambiente com [Tabelas e regras de firewall](firewall.md).
* Permitir o acesso da Internet de entrada para cargas de trabalho ao [Alocar IPs Públicos](public-ips.md).
* Permitir a conectividade a partir das suas redes internas ou estações de trabalho de cliente ao [configurar uma VPN](vpn-gateway.md).
* Permitir as comunicações a partir dos seus [ambientes no local](on-premises-connection.md) e, também, para as [Redes virtuais do Azure](virtual-network-connection.md).
* Configurar destinos de alerta e ver a capacidade total adquirida no [resumo da conta](account.md)
* Ver os [utilizadores](users.md) que acederam ao portal da CloudSimple.
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
