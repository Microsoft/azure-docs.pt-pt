---
title: Soluções Oracle em Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as opções para implantar aplicativos e soluções Oracle em Microsoft Azure, incluindo a execução completa na infraestrutura do Azure ou o uso da conectividade entre nuvem com o Oracle Cloud Infrastructure (OCI).
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
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: 6ab41182d59f8c96edfca4854aad5f9a13f53436
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806614"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral de aplicativos e soluções Oracle no Azure

Este artigo apresenta recursos para executar soluções Oracle usando a infraestrutura do Azure. Consulte também introduções detalhadas sobre as [imagens de VM Oracle](oracle-vm-solutions.md) disponíveis no Azure Marketplace e o recurso de visualização para [interconectar o Azure ao Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bancos de dados Oracle na infraestrutura do Azure

Execute bancos de dados Oracle na infraestrutura do Azure usando Oracle Database em Oracle Linux imagens disponíveis no Azure Marketplace:

* Oracle Database 12,1, 12,2 e 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 e 18,3 Standard Edition 

Você também pode optar por configurar Oracle Database em uma imagem não Oracle Linux disponível no Azure, basear uma solução em uma imagem personalizada criada a partir do zero no Azure ou carregar uma imagem personalizada do seu ambiente local.

Opcionalmente, configure com vários discos anexados e aprimore o desempenho do banco de dados instalando o ASM (Automated Storage Management) da Oracle.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicativos no Oracle Linux e no WebLogic Server

Execute aplicativos empresariais no Azure em sistemas operacionais Oracle com suporte. As seguintes imagens estão disponíveis no Azure Marketplace:

* 12.1.2 do Oracle WebLogic Server

* Oracle Linux com o UEK (inquebrable Enterprise kernel) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 e 7,6 

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de alta disponibilidade e recuperação de desastre

* Configure o [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), o [Active Data Guard com FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [fragmentação](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) ou [portão dourado](https://www.oracle.com/middleware/technologies/goldengate.html) na infraestrutura do Azure em conjunto com [zonas de disponibilidade](../../../availability-zones/az-overview.md) para alta disponibilidade na região. Você também pode configurar essas configurações em várias regiões do Azure para aumentar a disponibilidade e a recuperação de desastres.

* Use [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerenciar a recuperação de desastres para suas VMs Oracle Linux no Azure e seus servidores físicos ou locais. 

* Habilite RAC (clusters de aplicativos reais) da Oracle no Azure usando a [solução VMware do Azure](https://docs.azure.cloudsimple.com/oracle-rac/) ou [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Fazer backup de cargas de trabalho do Oracle

* Fazer backup de suas VMs Oracle usando o [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview)

* Faça backup de seu Oracle Database usando o Oracle RMAN e, opcionalmente, use o [fusível de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) para montar uma [conta de armazenamento de BLOBs do Azure altamente Redudant](https://docs.microsoft.com/azure/storage/common/storage-redundancy) e gravar os backups do RMAN nele para obter resiliência adicional.

## <a name="integration-of-azure-with-oci-preview"></a>Integração do Azure com o OCI (versão prévia)

Execute aplicativos Oracle na infraestrutura do Azure, conectados a bancos de dados de back-end no Oracle Cloud Infrastructure (OCI). Essa solução usa os seguintes recursos: 

* **Rede entre redes em nuvem** -use a interconexão direta disponível entre o Azure ExpressRoute e o Oracle FastConnect para estabelecer conexões de alta largura de banda, privada e baixa latência entre o aplicativo e a camada de banco de dados.
* **Identidade integrada** -configure a identidade federada entre o Azure AD e o Oracle IDCS para criar uma origem de identidade única para as soluções. Habilite o logon único para gerenciar recursos no OCI e no Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implantar aplicativos Oracle no Azure

Use modelos Terraform para configurar a infraestrutura do Azure e instalar aplicativos Oracle validados e com suporte para execução na configuração de nuvem cruzada:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo da Oracle
* Gerenciamento financeiro do Oracle Hyperion

Implante também aplicativos personalizados no Azure que se conectam ao OCI e a outros serviços do Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurar bancos de dados Oracle em OCI

Use Oracle Database serviços de nuvem (banco de dados autônomo, RAC, Exadata, DBaaS, nó único) em conjunto com os aplicativos Oracle em execução no Azure. Saiba mais sobre [as opções de banco de dados de OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenças

A implantação de aplicativos Oracle no Azure é baseada em um modelo "Traga sua própria licença". Supõe-se que você esteja devidamente licenciado para usar o software Oracle e que tenha um contrato de suporte atual em vigor com a Oracle. A Oracle assegurau a mobilidade de licenças do local para o Azure. Consulte as [perguntas frequentes sobre](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)o Oracle-Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implantar [imagens de VM do Oracle](oracle-vm-solutions.md) na infraestrutura do Azure.

* Saiba mais sobre como [interconectar o Azure com o OCI](oracle-oci-overview.md).

* Confira a [sessão de visão geral do Oracle no Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) do Ignite 2019. 
