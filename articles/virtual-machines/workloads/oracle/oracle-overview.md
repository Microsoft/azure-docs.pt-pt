---
title: Soluções Oracle no Microsoft Azure | Microsoft Docs
description: Saiba mais sobre opções para implementar aplicações e soluções oracle no Microsoft Azure, incluindo executar inteiramente na infraestrutura Azure ou usar conectividade cross-cloud com Oracle Cloud Infrastructure (OCI).
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 971f7c919595f915451faf9266ee3bb18b35087e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677169"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral das aplicações e soluções da Oracle no Azure

Este artigo introduz capacidades para executar soluções Oracle usando a infraestrutura Azure. Consulte também as apresentações detalhadas das [aplicações disponíveis do WebLogic Server Azure, imagens](oracle-weblogic.md) [Oracle VM](oracle-vm-solutions.md) no Mercado Azure e a capacidade de [interligar o Azure com a Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bases de dados da Oracle sobre a infraestrutura Azure

Executar bases de dados oracle na infraestrutura Azure usando a Oracle Database em imagens Oracle Linux disponíveis no Azure Marketplace:

* Oracle Database 12.1, 12.2 e 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 e 18.3 Edição Padrão

* Banco de Dados Oracle 19.3

Também pode optar por configurar o Oracle Database numa imagem não-Oracle Linux disponível no Azure, basear uma solução numa imagem personalizada que cria de raiz em Azure ou fazer upload de uma imagem personalizada a partir do seu ambiente no local.

Configurar opcionalmente com vários discos anexados e melhorar o desempenho da base de dados instalando a Oracle Automated Storage Management (ASM).

## <a name="weblogic-server-with-azure-service-integrations"></a>Servidor WebLogic com Integrações de ServiçoS Azure

Escolha entre uma variedade de Aplicações WebLogic Server Azure para acelerar a sua jornada em nuvem.  Estão disponíveis várias integrações de serviços Azure pré-configuradas, incluindo base de dados, Azure App Gateway e Azure Ative Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicações no Oracle Linux e WebLogic Server

Executar aplicações empresariais em Azure em imagens suportadas oracle Linux. As seguintes imagens de máquinas virtuais estão disponíveis no Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux com o Kernel da Empresa Inquebrável (UEK) 6.8, 6.9, 6.10, 7.3 através de 7.7, 8.0, 8.1. 

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de alta disponibilidade e recuperação de desastres

* Configure [a Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), Ative Data Guard com [FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) ou [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) na infraestrutura Azure em conjunto com [as Zonas de Disponibilidade](../../../availability-zones/az-overview.md) para uma elevada disponibilidade na região. Também pode configurar estas configurações em várias regiões do Azure para uma maior disponibilidade e recuperação de desastres.

* Utilize [a Recuperação do Site Azure](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerir a recuperação de desastres para os seus VMs Oracle Linux em Azure e seus servidores físicos. 

* Ativar os Clusters de Aplicações Reais da Oracle (RAC) em Azure utilizando [a Solução VMware Azure](../../../vmware-cloudsimple/oracle-real-application-clusters.md) ou [o FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Cargas de trabalho do Oráculo de Reserva

* Faça backup dos seus VMs Oráculos utilizando [o Backup Azure](../../../backup/backup-overview.md)

* Faça backup da sua Oracle Database utilizando o Oracle RMAN e utilize opcionalmente [o Azure Blob Fuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) para montar uma [conta de armazenamento Azure Blob altamente redundante](../../../storage/common/storage-redundancy.md) e escreva as suas cópias de segurança RMAN para obter uma maior resiliência.

## <a name="integration-of-azure-with-oci"></a>Integração do Azure com OCI

Executar aplicações Oracle em infraestrutura Azure, ligadas a bases de dados backend em Oracle Cloud Infrastructure (OCI). Esta solução utiliza as seguintes capacidades: 

* **Rede de nuvem cruzada** - Utilize a interligação direta disponível entre a Azure ExpressRoute e a Oracle FastConnect para estabelecer ligações de alta largura de banda, privada e baixa latência entre a aplicação e a camada de base de dados.
* **Identidade integrada** - Criar identidade federada entre a Azure AD e a Oracle IDCS para criar uma única fonte de identidade para as soluções. Permitir um único sign-on para gerir recursos em todo o OCI e Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implementar aplicações oracle em Azure

Utilize modelos Terraform para configurar a infraestrutura Azure e instalar aplicações oracle. 

A Oracle certificou estas aplicações para funcionar em Azure ao ligar-se a uma base de dados oracle através da solução de interligação Azure/Oracle Cloud:

* Suíte E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicações Oracle Retail
* Oracle Hyperion Gestão Financeira

Também implemente aplicações personalizadas no Azure que se conectam com o OCI e outros serviços Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Criar bases de dados oracle em OCI

Utilize os Serviços de Nuvem oracle Database (Base de Dados Autónoma, RAC, Exadata, DBaaS, Nó Único) em conjunto com o software Oracle em execução em Azure. Saiba mais sobre [as opções de base de dados de OCI.](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm) 
 

## <a name="licensing"></a>Licensing

A implementação de aplicações oracle em Azure baseia-se num modelo de "traga a sua própria licença". Presume-se que está devidamente licenciado para usar o software Oracle e que tem um acordo de apoio atual em vigor com a Oracle. A Oracle garantiu a mobilidade de licenças de instalações para Azure. Veja as [faq Oracle-Azure.](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [aplicações Azure do WebLogic Server](oracle-weblogic.md) e as integrações de serviços Azure que suportam.

* Saiba mais sobre a implementação de [imagens Oracle VM](oracle-vm-solutions.md) na infraestrutura Azure.

* Saiba mais sobre como [interligar Azure com OCI](oracle-oci-overview.md).

* Confira a sessão geral do [Oráculo em Azure](https://www.pluralsight.com/courses/microsoft-ignite-session-57) da Ignite 2019. 
