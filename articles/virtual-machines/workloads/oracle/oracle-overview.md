---
title: Soluções Oracle no Microsoft Azure Microsoft Docs
description: Saiba mais sobre as opções para implementar aplicações e soluções Oracle no Microsoft Azure, incluindo executar inteiramente em infraestrutura Azure ou usar conectividade transversal com a Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: rogirdh
ms.openlocfilehash: 101cfc9edce8f4df1ad2388c08a5bd9702dffe68
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878226"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral das aplicações e soluções da Oracle no Azure

Este artigo introduz capacidades para executar soluções Oracle utilizando a infraestrutura Azure. Consulte também as apresentações detalhadas às [imagens VM vm](oracle-vm-solutions.md) disponíveis no Mercado Azure e a capacidade de [interligar o Azure com a Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bases de dados da Oracle sobre infraestruturas Azure

Executar bases de dados da Oracle sobre a infraestrutura Azure utilizando a Base de Dados Oracle em imagens Oracle Linux disponíveis no Azure Marketplace:

* Oracle Base de Dados 12.1, 12.2 e 18.3 Enterprise Edition 

* Oracle Base de Dados 12.1, 12.2 e 18.3 Edição Standard 

Também pode optar por configurar a Oracle Database numa imagem não-Oracle Linux disponível no Azure, basear uma solução numa imagem personalizada que cria a partir do zero no Azure ou fazer upload de uma imagem personalizada do seu ambiente no local.

Configure opcionalmente com vários discos anexados e melhore o desempenho da base de dados instalando a Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicações no Oracle Linux e No Servidor WebLogic

Executar aplicações empresariais em Azure em sistemas operativos Oracle suportados. As seguintes imagens estão disponíveis no Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux com o Unbreakable Enterprise Kernel (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de alta disponibilidade e recuperação de desastres

* Configure [A oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), Ative Data Guard com [FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) ou [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) na infraestrutura Azure em conjunto com Zonas [de Disponibilidade](../../../availability-zones/az-overview.md) para alta disponibilidade na região. Também pode configurar estas configurações em várias regiões do Azure para maior disponibilidade e recuperação de desastres.

* Utilize a [Recuperação do Site Azure](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerir a recuperação de desastres para os seus VMs Oracle Linux em Azure e seus servidores ou servidores físicos no local. 

* Ativar clusters de aplicações real oracle (RAC) em Azure utilizando a [Solução Azure VMWare](https://docs.azure.cloudsimple.com/oracle-rac/) ou [o SkyCluster FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Cargas de trabalho do Oráculo de Reserva

* Backup seus VMs Oracle usando [backup Azure](https://docs.microsoft.com/azure/backup/backup-overview)

* Faça backup a sua Base de Dados Oracle utilizando o Oracle RMAN e utilize opcionalmente o [Fusão Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) para montar uma conta de armazenamento de [Blob Azure altamente reduda](https://docs.microsoft.com/azure/storage/common/storage-redundancy) e escreva-lhe as suas cópias de segurança RMAN para maior resiliência.

## <a name="integration-of-azure-with-oci"></a>Integração do Azure com OCI

Executar aplicações Oracle em infraestrutura seleção azure, ligadas a bases de dados de backend em Oracle Cloud Infrastructure (OCI). Esta solução utiliza as seguintes capacidades: 

* **Rede transversal** - Utilize a interligação direta disponível entre o Azure ExpressRoute e o Oracle FastConnect para estabelecer ligações de alta largura de banda, privadas e de baixa latência entre a aplicação e a camada de base de dados.
* **Identidade integrada** - Criar identidade federada entre a Azure AD e a Oracle IDCS para criar uma única fonte de identidade para as soluções. Permitir um único sign-on para gerir recursos através do OCI e do Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implementar aplicações oracle em Azure

Utilize modelos Terraform para configurar a infraestrutura Azure e instalar aplicações Oracle. 

> [!IMPORTANT]
> A Oracle certificará estas aplicações para funcionar em Azure quando utilizar a solução de interligação Azure/Oracle Cloud até maio de 2020.

* Suíte E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicações oracle Retail
* Gestão Financeira Oracle Hyperion

Também implemente aplicações personalizadas no Azure que se conectem com o OCI e outros serviços Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurar bases de dados oracle no OCI

Utilize os Serviços de Nuvem da Oracle Database (Base de Dados Autónoma, RAC, Exadata, DBaaS, Nó Único) em conjunto com as Aplicações Oracle em funcionamento em Azure. Saiba mais sobre as opções de [base de dados do OCI.](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm) 
 

## <a name="licensing"></a>Licenciamento

A implementação da Oracle Applications em Azure baseia-se num modelo de "trazer a sua própria licença". Presume-se que está devidamente licenciado para usar o software Oracle e que tem um acordo de suporte atual com a Oracle. A Oracle garantiu a mobilidade de licença saindo do local para O Azure. Consulte o Oráculo-Azure [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a implementação de [imagens Oracle VM](oracle-vm-solutions.md) na infraestrutura azure.

* Saiba mais sobre como [interligar Azure com OCI.](oracle-oci-overview.md)

* Confira a sessão de visão geral do [Oracle em Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) da Ignite 2019. 