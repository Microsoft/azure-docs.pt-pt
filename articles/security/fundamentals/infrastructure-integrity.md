---
title: Integridade da infraestrutura azure
description: Este artigo aborda a integridade da infraestrutura Azure.
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
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "68727194"
---
# <a name="azure-infrastructure-integrity"></a>Integridade da infraestrutura azure

## <a name="software-installation"></a>Instalação de software
Todos os componentes da pilha de software que estão instalados no ambiente Azure são construídos sob medida após o processo microsoft Security Development Lifecycle (SDL). Todos os componentes do software, incluindo imagens do sistema operativo (OS) e a Base de Dados SQL, são implantados como parte do processo de gestão de alterações e de gestão de lançamentos. O SISTEMA que funciona em todos os nós é uma versão personalizada do Windows Server 2008 ou do Windows Server 2012. A versão exata é escolhida pelo controlador de tecido (FC) de acordo com o papel que pretende desempenhar para o OS. Além disso, o osso hospedeiro não permite a instalação de quaisquer componentes de software não autorizados.

Alguns componentes Do Azure são implantados como clientes Azure em um VM convidado em execução em um OS convidado.

## <a name="virus-scans-on-builds"></a>O vírus digitaliza as construções
O componente de software Azure (incluindo o OS) tem de ser submetido a uma varredura de vírus que utiliza a ferramenta antivírus Endpoint Protection. Cada varredura de vírus cria um log dentro do diretório de construção associado, detalhando o que foi digitalizado e os resultados da digitalização. A varredura do vírus faz parte do código fonte de construção de todos os componentes dentro do Azure. O código não é movido para a produção sem ter uma varredura de vírus limpa e bem sucedida. Se algum problema for adotado, a construção está congelada e depois vai para as equipas de segurança dentro da Microsoft Security para identificar onde o código "fraudulento" entrou na construção.

## <a name="closed-and-locked-environment"></a>Ambiente fechado e fechado
Por padrão, os nós de infraestrutura Azure e os VMs convidados não têm contas de utilizador criadas sobre eles. Além disso, as contas de administrador do Windows por defeito também são desativadas. Os administradores do suporte ao vivo do Azure podem, com a autenticação adequada, entrar nestas máquinas e administrar a rede de produção azure para reparações de emergência.

## <a name="azure-sql-database-authentication"></a>Autenticação da Base de Dados SQL do Azure
Tal como acontece com qualquer implementação do SQL Server, a gestão da conta de utilizador deve ser controlada de forma rigorosa. A Base de Dados Azure SQL suporta apenas a autenticação do Servidor SQL. Para complementar o modelo de segurança de dados de um cliente, as contas de utilizador com senhas fortes e configuradas com direitos específicos também devem ser utilizadas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACLs e firewalls entre a rede corporativa da Microsoft e um cluster Azure
As listas de controlo de acesso (ACLs) e as firewalls entre a plataforma de serviço e a rede corporativa da Microsoft protegem as instâncias da Base de Dados SQL de acesso privilegiado não autorizado. Além disso, apenas os utilizadores de endereços IP variam a partir da rede corporativa da Microsoft pode aceder ao ponto final de gestão da plataforma Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACLs e firewalls entre nós em um cluster de base de dados SQL
Como proteção adicional, como parte da estratégia de profundidade de defesa, os ACLs e uma firewall foram implementados entre nós num cluster de base de dados SQL. Toda a comunicação dentro do cluster da plataforma Windows Fabric, bem como todo o código de execução, são confiáveis.

## <a name="custom-monitoring-agents"></a>Agentes de monitorização personalizados
A SQL Database emprega agentes de monitorização personalizados (MAs), também chamados de cães de guarda, para monitorizar a saúde do cluster de base de dados SQL.

## <a name="web-protocols"></a>Protocolos web

### <a name="role-instance-monitoring-and-restart"></a>Monitorização e reinício de instâncias de funções
O Azure garante que todas as funções implementadas, executantes (web viradas para a Internet ou funções de trabalhadores de processamento de back-end) estão sujeitas a uma monitorização sustentada da saúde para garantir que fornecem de forma eficaz e eficiente os serviços para os quais foram prestados. Se um papel se tornar insalubre, por uma falha crítica na aplicação que está a ser hospedada ou por um problema de configuração subjacente dentro da própria instância de função, o FC deteta o problema dentro da instância de função e inicia um estado corretivo.

### <a name="compute-connectivity"></a>Conectividade computacional
O Azure garante que a aplicação ou serviço implementado é acessível através de protocolos padrão baseados na Web. As instâncias virtuais de funções web viradas para a Internet têm conectividade externa da Internet e são acessíveis diretamente pelos utilizadores da web. Para proteger a sensibilidade e integridade das operações que os papéis dos trabalhadores desempenham em nome do papel web acessível ao público, as instâncias virtuais das funções de trabalhadores de processamento de back-end têm conectividade externa da Internet, mas não podem ser acedidas diretamente por utilizadores externos da web.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)
