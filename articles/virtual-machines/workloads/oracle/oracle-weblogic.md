---
title: Aplicações Oracle WebLogic Server Azure / Microsoft Docs
description: Saiba como executar o Oracle WebLogic Server no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665216"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Aplicações Oráculos WebLogic Server Azure

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>O Oracle WebLogic Server é um servidor de aplicação Java EE pronto para a empresa.

O Oracle WebLogic Server é o primeiro servidor de aplicações da plataforma java nativa da nuvem do mundo para desenvolver e implementar aplicações empresariais distribuídas por vários níveis. As ofertas do Azure WebLogic Server permitem-lhe abraçar a computação em nuvem, proporcionando uma maior escolha e flexibilidade para a migração WebLogic, incluindo o levantamento e mudança das suas aplicações Java EE para azure Cloud com o menor esforço e maior impacto. As ofertas capacitam-no a iniciar rapidamente as suas aplicações de negócio, disponibilizando automaticamente recursos de rede virtual, armazenamento e Linux, instalando o WebLogic Server, instalando segurança com um grupo de segurança de rede, equilibrando a carga com o Azure App Gateway, autenticação com Diretório Ativo Azure e facilitando a conectividade da base de dados.

Existem quatro ofertas disponíveis para atender diferentes cenários: nó único sem servidor de administração, nó único com servidor de administração, cluster e cluster dinâmico.  Você deve se sentir livre para dar-lhes uma tentativa, as ofertas estão disponíveis gratuitamente.

_Estas ofertas são Bring-Your-Own-License_. Assumem que já obteve as licenças apropriadas com a Oracle e que está devidamente licenciado para executar ofertas no Microsoft Azure.

_Se quiser trabalhar em estreita colaboração nos seus cenários de migração com a equipa de engenharia a desenvolver estas ofertas, basta carregar no botão [CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Gestores de programas, arquitetos e engenheiros irão contactar-lhe em breve e iniciar a colaboração!

### <a name="oracle-weblogic-server-single-node"></a>Nó único do servidor weblógico oracle

Esta oferta disponibiliza uma única máquina virtual e instala o Oracle WebLogic Server. Não cria um domínio nem inicia o Servidor de Administração. Isto é útil para cenários com configuração de domínio altamente personalizada.

### <a name="oracle-weblogic-server-with-admin-server"></a>Servidor Oracle WebLogic com Servidor De Administrador

Esta oferta disponibiliza uma única máquina virtual e instala o Oracle WebLogic Server. Cria um domínio e inicia o Servidor de Administração, que lhe permite gerir o domínio.

### <a name="oracle-weblogic-server-cluster"></a>Cluster de servidor weblógico oracle

Esta oferta cria um aglomerado altamente disponível de máquinas virtuais do Oracle WebLogic Server. O Servidor de Administração e todos os servidores geridos são iniciados por padrão, que lhe permitem gerir o domínio.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster dinâmico do servidor weblógico oracle

Esta oferta cria um cluster dinâmico altamente disponível e escalável de máquinas virtuais do Oracle WebLogic Server. O Servidor de Administração e todos os servidores geridos são iniciados por padrão, que lhe permitem gerir o domínio.

## <a name="next-steps"></a>Passos seguintes

Explore as ofertas no Azure Marketplace.

> [!div class="nextstepaction"]
> [Nó único do servidor weblógico oracle](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Servidor Oracle WebLogic com Servidor De Administrador](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Cluster de servidor weblógico oracle](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster dinâmico do servidor weblógico oracle](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
