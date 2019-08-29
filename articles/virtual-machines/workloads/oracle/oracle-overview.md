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
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100013"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral de aplicativos e soluções Oracle no Azure

Este artigo apresenta recursos para executar soluções Oracle usando a infraestrutura do Azure. Consulte também introduções detalhadas sobre as [imagens de VM Oracle](oracle-vm-solutions.md) disponíveis no Azure Marketplace e o recurso de visualização para interconectar o [Azure ao Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bancos de dados Oracle na infraestrutura do Azure

Execute bancos de dados Oracle na infraestrutura do Azure usando as imagens do Linux disponíveis no Azure Marketplace:

* Oracle Database 12,1, 12,2 e 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 e 18,3 Standard Edition 

Você também pode optar por basear uma solução em uma imagem personalizada criada do zero no Azure ou carregar uma imagem personalizada do seu ambiente local.

Opcionalmente, configure com vários discos anexados e aprimore o desempenho do banco de dados instalando o ASM (Automated Storage Management) da Oracle.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicativos no Oracle Linux e no WebLogic Server

Execute aplicativos empresariais no Azure em sistemas operacionais Oracle com suporte. As seguintes imagens estão disponíveis no Azure Marketplace:

* 12.1.2 do Oracle WebLogic Server

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 e 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de alta disponibilidade e recuperação de desastre

* Configure o Oracle Data Guard, o Active Data Guard ou o GoldenGate na infraestrutura do Azure em conjunto com [zonas de disponibilidade](../../../availability-zones/az-overview.md) para alta disponibilidade.

* Use [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerenciar a recuperação de desastres para suas VMs Oracle Linux no Azure e seus servidores físicos ou locais. 

* Habilite RAC (clusters de aplicativos reais) da Oracle no Azure usando [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

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
 

## <a name="licensing"></a>Licenciamento

A implantação de aplicativos Oracle no Azure é baseada em um modelo "Traga sua própria licença". Supõe-se que você esteja devidamente licenciado para usar o software Oracle e que tenha um contrato de suporte atual em vigor com a Oracle. A Oracle assegurau a mobilidade de licenças do local para o Azure. Consulte as [perguntas frequentes sobre](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)o Oracle-Azure.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como implantar [imagens de VM do Oracle](oracle-vm-solutions.md) na infraestrutura do Azure.

* Saiba mais sobre como interconectar o [Azure com o OCI](oracle-oci-overview.md).