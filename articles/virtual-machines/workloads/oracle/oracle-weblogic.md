---
title: Aplicações Azure do Servidor WebLogic da Oracle Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83665216"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Aplicações oracle WebLogic Server Azure

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>O Oracle WebLogic Server é um servidor de aplicações Java EE escalável e pronto para empresas.

O Oracle WebLogic Server é o primeiro servidor de aplicações de plataforma java nativo da nuvem do mundo para desenvolver e implementar aplicações empresariais distribuídas em vários níveis. O Azure WebLogic Server oferece-lhe para abraçar a computação em nuvem, proporcionando uma maior escolha e flexibilidade para a migração da WebLogic, incluindo levantar e transferir as suas aplicações Java EE para a Azure Cloud com o menor esforço e maior impacto. As ofertas capacitam-no a iniciar rapidamente as suas aplicações empresariais, fornecendo automaticamente recursos de rede virtual, armazenamento e Linux, instalação do WebLogic Server, criação de segurança com um grupo de segurança de rede, carregamento de equilíbrio com Gateway de aplicações Azure, autenticação com Diretório Ativo Azure e facilitação da conectividade da base de dados.

Existem quatro ofertas disponíveis para atender diferentes cenários: nó único sem um servidor de administração, nó único com um servidor de administração, cluster e cluster dinâmico.  Você deve se sentir livre para dar-lhes uma tentativa, as ofertas estão disponíveis gratuitamente.

_Estas ofertas são "Bring-Your-Own-License"._ Eles assumem que você já obteve as licenças apropriadas com a Oracle e estão devidamente licenciados para executar ofertas no Microsoft Azure.

_Se quiser trabalhar de perto nos seus cenários de migração com a equipa de engenharia que desenvolve estas ofertas, basta premir o botão [CONTACTE ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Gestores de programas, arquitetos e engenheiros irão contactá-lo em breve e iniciar a colaboração!

### <a name="oracle-weblogic-server-single-node"></a>Nó único do servidor weblogic do Oracle

Esta oferta fornece uma única máquina virtual e instala o Oracle WebLogic Server nele. Não cria um domínio nem inicia o Servidor de Administração. Isto é útil para cenários com configuração de domínio altamente personalizada.

### <a name="oracle-weblogic-server-with-admin-server"></a>Servidor WebLogic da Oracle com Servidor de Administração

Esta oferta fornece uma única máquina virtual e instala o Oracle WebLogic Server nele. Cria um domínio e inicia o Servidor de Administração, que lhe permite gerir o domínio.

### <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server Cluster

Esta oferta cria um cluster altamente disponível de máquinas virtuais Oracle WebLogic Server. O Servidor de Administração e todos os servidores geridos são iniciados por padrão, o que lhe permite gerir o domínio.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster Dinâmico do Servidor WebLogic da Oracle

Esta oferta cria um cluster dinâmico altamente disponível e escalável de máquinas virtuais Oracle WebLogic Server. O Servidor de Administração e todos os servidores geridos são iniciados por padrão, o que lhe permite gerir o domínio.

## <a name="next-steps"></a>Próximos passos

Explore as ofertas no Azure Marketplace.

> [!div class="nextstepaction"]
> [Nó único do servidor weblogic do Oracle](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Servidor WebLogic da Oracle com Servidor de Administração](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster Dinâmico do Servidor WebLogic da Oracle](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
