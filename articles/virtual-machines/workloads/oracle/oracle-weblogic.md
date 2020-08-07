---
title: O que é o Oracle WebLogic Server em Azure?
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
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851868"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>O que é o Oracle WebLogic Server em Azure?

Esta página descreve soluções para executar o WebLogic Server (WLS) em Azure Virtual Machines.  Estas soluções são desenvolvidas conjuntamente pela Oracle e Microsoft.

O Oracle WebLogic Server é o primeiro servidor de aplicações de plataforma java nativo da nuvem do mundo para desenvolver e implementar aplicações empresariais distribuídas em vários níveis. As ofertas do Azure WebLogic Server permitem-lhe abraçar a computação em nuvem.  Obtém-se uma maior escolha e flexibilidade para a migração da WebLogic, incluindo o levantamento e a mudança das suas aplicações Java EE para a Azure Cloud.   A WLS em Azure produz um grande impacto com um pequeno esforço. As ofertas capacitam-no a iniciar rapidamente as suas aplicações de linha de negócios (LOB).  Cada oferta fornece automaticamente recursos de rede virtual, armazenamento e Linux.  Sem esforço, o WebLogic Server está instalado.  A WLS on Azure configura a segurança com um grupo de segurança de rede, o equilíbrio de carga com o Azure App Gateway, a autenticação com o Azure Ative Directory e liga-se automaticamente à base de dados existente.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Pode utilizar o Portal Azure para implementar o Servidor WebLogic no Azure":::

Existem quatro ofertas disponíveis para atender diferentes cenários: nó único sem um servidor de administração, nó único com um servidor de administração, cluster e cluster dinâmico.  Experimente as ofertas, estão disponíveis gratuitamente.

_Estas ofertas são "Bring-Your-Own-License"._ Eles assumem que você já tem as licenças apropriadas com a Oracle e estão devidamente licenciados para executar ofertas no Microsoft Azure.

_Se pretender trabalhar de perto nos seus cenários de migração com a equipa de engenharia que desenvolve estas ofertas, selecione o botão [CONTACTE ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Gestores de programas, arquitetos e engenheiros vão contactar-te em breve e iniciar a colaboração!

## <a name="oracle-weblogic-server-single-node"></a>Nó único do servidor weblogic do Oracle

Esta oferta fornece uma única máquina virtual e instala WLS nela. Não cria um domínio nem inicia o Servidor de Administração. O nó único é útil para cenários com configuração de domínio altamente personalizada.

## <a name="oracle-weblogic-server-with-admin-server"></a>Servidor WebLogic da Oracle com Servidor de Administração

Esta oferta fornece uma única máquina virtual e instala WLS nela. Cria um domínio e inicia o Servidor de Administração, que lhe permite gerir o domínio.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server Cluster

Esta oferta cria um cluster altamente disponível de máquinas virtuais WLS. O Servidor de Administração e todos os servidores geridos são iniciados por padrão, o que lhe permite gerir o domínio.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster Dinâmico do Servidor WebLogic da Oracle

Esta oferta cria um cluster dinâmico altamente disponível e escalável de máquinas virtuais WLS. O Servidor de Administração e todos os servidores geridos são iniciados por padrão, o que lhe permite gerir o domínio.

## <a name="next-steps"></a>Passos seguintes

Explore as ofertas no Azure Marketplace.

> [!div class="nextstepaction"]
> [Nó único do servidor weblogic do Oracle](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Servidor WebLogic da Oracle com Servidor de Administração](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster Dinâmico do Servidor WebLogic da Oracle](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
