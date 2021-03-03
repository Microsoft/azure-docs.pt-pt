---
title: Quais são as soluções para executar o Servidor WebLogic da Oracle em Máquinas Virtuais Azure
description: Saiba como executar o Oracle WebLogic Server nas Máquinas Virtuais Microsoft Azure.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/24/2021
ms.author: rezar
ms.openlocfilehash: a5675b313586615d4bad733aec6eabf0360f8489
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694716"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Quais são as soluções para executar o Oracle WebLogic Server nas Máquinas Virtuais do Azure?

Esta página descreve as soluções para executar o Oracle WebLogic Server (WLS) em máquinas virtuais Azure. Estas soluções são desenvolvidas e apoiadas conjuntamente pela Oracle e Microsoft.

Também pode executar WLS no Serviço Azure Kubernetes. As soluções para o fazer são descritas [neste artigo da Microsoft](./weblogic-aks.md).

A WLS é um servidor de aplicações java líder que executa algumas das aplicações java mais críticas da missão em todo o mundo. A WLS forma a base de middleware para a suite de software Oracle. A Oracle e a Microsoft estão empenhadas em capacitar os clientes da WLS com escolha e flexibilidade para executar cargas de trabalho no Azure como uma plataforma de nuvem líder.

As soluções Azure WLS destinam-se a facilitar ao máximo a migração das suas aplicações Java para máquinas virtuais Azure. As soluções fazem-no gerando recursos implantados para os cenários de avisionamento em nuvem mais comuns. As soluções fornecem automaticamente recursos de rede virtual, armazenamento, Java, WLS e Linux. Com o mínimo de esforço, o WebLogic Server está instalado. As soluções podem criar segurança com um grupo de segurança de rede, equilibrar carga com Azure App Gateway, autenticação com Azure Ative Directory, registo centralizado usando ELK e caching distribuído com a Oracle Coerência. Também pode ligar-se automaticamente à sua base de dados existente, incluindo Azure PostgreSQL, Azure SQL e Oracle DB na Oracle Cloud ou Azure. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Pode utilizar o portal Azure para implementar o Servidor WebLogic no Azure":::

Existem quatro ofertas disponíveis para atender diferentes cenários: [nó único sem um servidor de administração,](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)nó único com um servidor de [administração,](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) [cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)e [cluster dinâmico.](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) As ofertas estão disponíveis gratuitamente. Estas ofertas são descritas e ligadas abaixo.

_Estas ofertas são "Bring-Your-Own-License"._ Eles assumem que você já tem as licenças apropriadas com a Oracle e estão devidamente licenciados para executar ofertas em Azure.

As ofertas suportam uma gama de versões de sistema operativo, Java e WLS através de imagens base (como WebLogic Server 14 e JDK 11 no Oracle Linux 7.6). Estas imagens base também estão disponíveis no Azure por si só. As imagens base são adequadas para clientes que requerem implementações Azure complexas e personalizadas. O conjunto atual de imagens base está disponível [aqui.](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)

_Se estiver interessado em trabalhar de perto nos seus cenários de migração com a equipa de engenharia que desenvolve estas ofertas, selecione o botão [CONTACTE ME](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ na página de [visão geral](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)da oferta do mercado . Gestores de programas, arquitetos e engenheiros vão contactá-lo em breve e iniciar uma estreita colaboração. A oportunidade de colaborar num cenário de migração é gratuita enquanto as ofertas estão em desenvolvimento ativo.

## <a name="oracle-weblogic-server-single-node"></a>Nó único do servidor weblogic do Oracle

[Esta oferta](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) fornece uma única máquina virtual e instala WLS nela. Não cria um domínio ou inicia o servidor de administração. A oferta de nó único é útil para cenários com configuração de domínio altamente personalizada.

## <a name="oracle-weblogic-server-with-admin-server"></a>Servidor WebLogic da Oracle com Servidor de Administração

[Esta oferta](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) fornece uma única máquina virtual e instala WLS nela. Cria um domínio e inicia o servidor de administração. Pode gerir o domínio e começar com as implementações da aplicação imediatamente.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server Cluster

[Esta oferta](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) cria um cluster altamente disponível de máquinas virtuais WLS. O servidor de administração e todos os servidores geridos são iniciados por padrão. Você pode gerir o cluster e começar com aplicações altamente disponíveis imediatamente.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster Dinâmico do Servidor WebLogic da Oracle

[Esta oferta](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) cria um cluster dinâmico altamente disponível e escalável de máquinas virtuais WLS. O servidor de administração e todos os servidores geridos são iniciados por padrão.

As soluções permitirão uma vasta gama de arquiteturas de implantação prontas a produção com relativa facilidade. Você pode atender a maioria dos casos de migração da forma mais produtiva possível, permitindo um foco no desenvolvimento de aplicações de negócio.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Implementações complexas do Servidor WebLogic são ativadas no Azure":::

Para além do que é automaticamente a provisionado pelas soluções, os clientes têm total flexibilidade para personalizar ainda mais as suas implementações. É provável que, além da implementação de aplicações, os clientes integrem mais recursos Azure com as suas implementações. Os clientes são encorajados a [conectar-se com a equipa de desenvolvimento](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) e fornecer feedback sobre a melhoria das soluções.

## <a name="next-steps"></a>Passos seguintes

Explore as ofertas em Azure.

> [!div class="nextstepaction"]
> [Nó único do servidor weblogic do Oracle](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Servidor WebLogic da Oracle com Servidor de Administração](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster Dinâmico do Servidor WebLogic da Oracle](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
