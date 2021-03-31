---
title: Integridade da infraestrutura azul
description: Conheça a integridade da infraestrutura Azure e os passos que a Microsoft toma para o proteger, como as análises de vírus nas construções de componentes de software.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543644"
---
# <a name="azure-infrastructure-integrity"></a>Integridade da infraestrutura azul

## <a name="software-installation"></a>Instalação de software
Todos os componentes da pilha de software que são instalados no ambiente Azure são construídos sob medida após o processo de Lifecycle (SDL) do Microsoft Security Development. Todos os componentes do software, incluindo imagens do sistema operativo (OS) e base de dados SQL, são implantados como parte do processo de gestão de alterações e gestão de lançamentos. O SISTEMA que funciona em todos os nós é uma versão personalizada do Windows Server 2008 ou do Windows Server 2012. A versão exata é escolhida pelo controlador de tecido (FC) de acordo com o papel que pretende que o SO jogue. Além disso, o sistema operativo anfitrião não permite a instalação de quaisquer componentes de software não autorizados.

Alguns componentes Azure são implantados como clientes Azure em um VM convidado em execução em um SO convidado.

## <a name="virus-scans-on-builds"></a>Scans de vírus em construções
O componente de software Azure (incluindo o SO) tem de ser submetido a uma varredura de vírus que utiliza a ferramenta antivírus endpoint Protection. Cada verificação de vírus cria um registo dentro do diretório de construção associado, detalhando o que foi digitalizado e os resultados da digitalização. A varredura do vírus faz parte do código fonte de construção para cada componente dentro do Azure. O código não é movido para a produção sem ter uma varredura de vírus limpa e bem sucedida. Se forem anotados problemas, a construção é congelada e, em seguida, vai para as equipas de segurança dentro da Microsoft Security para identificar onde o código "fraudulento" entrou na construção.

## <a name="closed-and-locked-environment"></a>Ambiente fechado e fechado
Por padrão, os nós de infraestrutura Azure e os VMs dos hóspedes não têm contas de utilizador criadas neles. Além disso, as contas de administrador do Windows padrão também são desativadas. Os administradores do suporte ao vivo da Azure podem, com a autenticação adequada, entrar nestas máquinas e administrar a rede de produção Azure para reparações de emergência.

## <a name="azure-sql-database-authentication"></a>Autenticação da Base de Dados SQL do Azure
Como em qualquer implementação do SQL Server, a gestão da conta do utilizador deve ser rigorosamente controlada. A Azure SQL Database suporta apenas a autenticação do SQL Server. Para complementar o modelo de segurança de dados de um cliente, as contas de utilizador com senhas fortes e configuradas com direitos específicos também devem ser utilizadas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACLs e firewalls entre a rede corporativa da Microsoft e um cluster Azure
As listas de controlo de acesso (ACLs) e as firewalls entre a plataforma de serviço e a rede corporativa da Microsoft protegem as instâncias sql database de acessos não autorizados a informações privilegiadas. Além disso, apenas os utilizadores a partir de endereços IP variam a partir da rede corporativa da Microsoft podem aceder ao ponto final de gestão da plataforma do Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACLs e firewalls entre nós num cluster de base de dados SQL
Como uma proteção adicional, como parte da estratégia de profundidade de defesa, ACLs e uma firewall foram implementados entre nós em um cluster sql base de dados. Toda a comunicação dentro do cluster da plataforma Windows Fabric, bem como todo o código de execução é confiável.

## <a name="custom-monitoring-agents"></a>Agentes de monitorização personalizados
A SQL Database emprega agentes de monitorização personalizados (MAs), também chamados de cães de guarda, para monitorizar a saúde do cluster sql Database.

## <a name="web-protocols"></a>Protocolos web

### <a name="role-instance-monitoring-and-restart"></a>Monitorização e reinício de instâncias de função
O Azure garante que todas as funções implementadas, executando (web virada para a Internet ou funções de trabalhador de processamento de back-end) estão sujeitas a uma monitorização de saúde sustentada para garantir que eles fornecem de forma eficaz e eficiente os serviços para os quais foram abastecados. Se um papel se tornar insalubre, quer por uma falha crítica na aplicação que está a ser hospedada ou por um problema de configuração subjacente dentro da própria instância de função, o FC deteta o problema dentro da fase de função e inicia um estado corretivo.

### <a name="compute-connectivity"></a>Conectividade computacional
O Azure garante que a aplicação ou serviço implantado é acessível através de protocolos padrão baseados na Web. Casos virtuais de funções web voltados para a Internet têm conectividade externa na Internet e são alcançáveis diretamente pelos utilizadores da web. Para proteger a sensibilidade e integridade das operações que as funções dos trabalhadores desempenham em nome das instâncias virtuais de funções web acessíveis ao público, os casos virtuais de funções de trabalhadores de processamento de back-end têm conectividade externa na Internet, mas não podem ser acedidos diretamente por utilizadores da web externos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
