---
title: Integridade de infraestrutura do Azure
description: Este artigo aborda a integridade da infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 24d54fa7a8985a6af58cddfc969b8023485c73c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587126"
---
# <a name="azure-infrastructure-integrity"></a>Integridade de infraestrutura do Azure

## <a name="software-installation"></a>Instalação de software
Todos os componentes da pilha de software que estão instalados no ambiente do Azure são personalizadas incorporadas ao seguir o processo do Microsoft Security Development Lifecycle (SDL). Todos os componentes de software, incluindo imagens do sistema operativo (SO) e base de dados SQL, são implementados como parte da gestão de alterações e processo de gerenciamento de versão. O sistema operacional que é executado em todos os nós é uma versão personalizada do Windows Server 2008 ou Windows Server 2012. A versão exata é escolhida pelo controlador de malha (FC), de acordo com a função tenciona para o sistema operativo reproduzir. Além disso, o sistema operacional host não permitir a instalação de quaisquer componentes de software não autorizado.

Alguns componentes do Azure são implementadas como os clientes do Azure num convidado de VM em execução num sistema operacional convidado.

## <a name="virus-scans-on-builds"></a>Verificações de vírus nas compilações
Compilações de componente (incluindo o sistema operacional) de software do Azure tem de passar por uma verificação de vírus que utiliza a ferramenta de software antivírus do Endpoint Protection. Cada verificação de vírus cria um registo no diretório build associado, com detalhes sobre o que foi analisado e os resultados da análise. A verificação de vírus faz parte do código-fonte compilação para cada componente no Azure. Código não é movido para produção sem ter um vírus limpo e com êxito de análise. Se todos os problemas são assinalados, a compilação é congelada e, em seguida, vai para as equipes de segurança na Microsoft Security para identificar em que o código "não autorizados" inserido a compilação.

## <a name="closed-and-locked-environment"></a>Ambiente de fechada e bloqueado
Por predefinição, nós de infraestrutura do Azure e máquinas virtuais convidadas não têm contas de utilizador criadas nos mesmos. Além disso, as contas de administrador do Windows padrão também estão desativadas. Os administradores de suporte em direto do Azure podem, com uma autenticação adequada, iniciar sessão nestas máquinas e administrar a rede de produção do Azure para reparações de emergência.

## <a name="azure-sql-database-authentication"></a>Autenticação de base de dados SQL do Azure
Tal como acontece com qualquer implementação do SQL Server, gestão de contas de utilizador deve ser rigidamente controlada. Base de dados SQL do Azure suporta apenas a autenticação do SQL Server. Para complementar o modelo de segurança de dados de um cliente, o utilizador de contas com palavras-passe fortes e configurado com específicas direitos também devem ser utilizados.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>As ACLs e as firewalls entre a rede corporativa da Microsoft e de um cluster do Azure
Listas de controlo de acesso (ACLs) e as firewalls entre a plataforma de serviço e a rede corporativa da Microsoft proteger instâncias de base de dados SQL de acesso não autorizado insider. Além disso, apenas os utilizadores de intervalos de endereços IP da rede corporativa da Microsoft podem aceder ao ponto de final de gerenciamento de plataforma de recursos de infraestrutura do Windows.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>As ACLs e as firewalls entre os nós num cluster de base de dados SQL
Como proteção adicional, como parte da defesa em profundidade-estratégia, têm sido implementadas ACLs e uma firewall entre os nós num cluster de base de dados SQL. Todas as comunicações dentro do cluster de plataforma de recursos de infraestrutura do Windows, bem como todo o código em execução é fidedigno.

## <a name="custom-monitoring-agents"></a>Agentes de monitorização de personalizado
Base de dados SQL emprega personalizados agentes de monitorização (MAs), também denominados watchdogs, para monitorizar o estado de funcionamento do cluster de base de dados SQL.

## <a name="web-protocols"></a>Protocolos de Web

### <a name="role-instance-monitoring-and-restart"></a>Monitorização de instâncias de função e reinício
Azure garante que todos implementado, executar funções (web com acesso à internet ou funções de trabalho de processamento de back-end) estão sujeitos a do Estado de funcionamento constante de monitorização para garantir que estes com eficiência e eficácia prestar os serviços para o qual já foi aprovisionados. Se uma função de ficar danificada, por uma falha crítica no aplicativo que está sendo hospedado ou um problema de configuração subjacente dentro da instância de função em si, o FC detectará o problema dentro da instância de função e inicia um Estado de correção.

### <a name="compute-connectivity"></a>Conectividade de computação
Azure garante que a aplicação implementada ou o serviço está acessível através de protocolos padrão baseada na web. Instâncias virtuais das funções da web com acesso à internet tem conectividade de internet externo e estão acessíveis diretamente pelos utilizadores do web. Para proteger a confidencialidade e a integridade das operações que funções de trabalho executam em nome das instâncias de virtual de função web acessível publicamente, virtual instâncias de funções de trabalho de processamento de back-end tem conectividade de internet externo, mas não podem ser acedido diretamente pelos utilizadores da web externos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)
