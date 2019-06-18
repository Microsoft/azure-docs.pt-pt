---
title: Soluções da Oracle no Microsoft Azure | Documentos da Microsoft
description: Saiba mais sobre as opções para implementar aplicações de Oracle e soluções no Microsoft Azure, incluindo executar inteiramente na infraestrutura do Azure ou utilizar a conectividade entre Clouds com infraestrutura de nuvem da Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743639"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral das aplicações Oracle e soluções no Azure

Este artigo apresenta capacidades para executar as soluções da Oracle com a infraestrutura do Azure. Consulte também introduções detalhadas para disponíveis [imagens de VM do Oracle](oracle-vm-solutions.md) no Azure Marketplace e a capacidade de pré-visualização de [interligações do Azure com infraestrutura de nuvem da Oracle (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bases de dados do Oracle na infraestrutura do Azure

Execute os bancos de dados Oracle na infraestrutura do Azure com imagens do Linux disponíveis no Azure Marketplace:

* Oracle Database 12.1 12.2 e 18,3 Enterprise Edition 

* Oracle Database 12.1 12.2 e 18,3 Standard Edition 

Também pode optar por uma solução de base numa imagem personalizada para criar do zero no Azure ou carregar uma imagem personalizada do seu ambiente no local.

Opcionalmente, configure com vários discos anexados e melhorar o desempenho de base de dados através da instalação Oracle automatizada Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicações no Linux da Oracle e WebLogic Server

Execute aplicações empresariais no Azure em sistemas de operativos Oracle. As seguintes imagens estão disponíveis no Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de recuperação após desastre e de disponibilidade elevadas

* Configurar o Oracle Data Guard, proteção de dados Active Directory ou GoldenGate na infraestrutura do Azure em conjunto com [zonas de disponibilidade](../../../availability-zones/az-overview.md) para elevada disponibilidade.

* Uso [do Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerir a recuperação após desastre para as VMs de Linux da Oracle no Azure e no local ou servidores físicos. 

* Ativar os Clusters de aplicativo Real da Oracle (RAC) no Azure com [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integração do Azure com o OCI (pré-visualização)

Execute aplicações de Oracle na infraestrutura do Azure e ligada a bases de dados de back-end na infraestrutura de nuvem da Oracle (OCI). Esta solução utiliza as seguintes capacidades: 

* **Rede entre Clouds** -uso direto interligações disponível entre o Azure ExpressRoute e Oracle FastConnect para estabelecer ligações de largura de banda alta, privadas e de baixa latência entre a aplicação e a camada de base de dados.
* **Integrado identidade** -configurar a identidade federada entre o Azure AD e o Oracle IDCS para criar uma origem de identidade única para as soluções. Ative o início de sessão único gerir os recursos em OCI e o Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implementar aplicações Oracle no Azure

Utilize o Terraform modelos para configurar a infraestrutura do Azure e instalar aplicações de Oracle validadas e suportada para executar a configuração entre Clouds:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo do Oracle
* Gerenciamento financeiro do Oracle Hyperion

Também implemente aplicativos personalizados no Azure que se ligam com OCI e outros serviços do Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurar bases de dados do Oracle no OCI

Utilize os serviços de Cloud de base de dados Oracle (base de dados autónomo, RAC, Exadata, DBaaS, nó único) em conjunto com aplicações Oracle em execução no Azure. Saiba mais sobre [opções de base de dados OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenciamento

Implementação de aplicações do Oracle no Azure baseia-se um modelo "traga a sua própria licença". Pressupõe-se que está corretamente licenciado para utilizar Oracle software e de que tem um contrato de suporte atual no local com a Oracle. Oracle tem garantia de mobilidade de licenças no local para o Azure. Veja o Azure de Oracle [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a implantação [imagens de VM do Oracle](oracle-vm-solutions.md) na infraestrutura do Azure.

* Saiba mais sobre como [interligações Azure com o OCI](oracle-oci-overview.md).