---
title: Servidor único do Advanced Threat Protection - base de dados do Azure para PostgreSQL-
description: Proteção avançada contra ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 81f42183276f95ddfb24fbdc388fef59acbe680e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073529"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Proteção avançada contra ameaças na base de dados do Azure para PostgreSQL - servidor único

A Proteção Avançada Contra Ameaças para Base de Dados do Azure para PostgreSQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares para aceder ou explorar as suas bases de dados.

> [!NOTE]
> Proteção avançada contra ameaças está em pré-visualização pública.

Proteção contra ameaças faz parte da oferta de proteção contra ameaças avançadas (ATP), que é um pacote unificado para os recursos de segurança avançada. Proteção avançada contra ameaças podem ser acessada e gerenciada através do [portal do Azure](https://portal.azure.com) ou utilizando [REST API](/rest/api/postgresql/serversecurityalertpolicies). A funcionalidade está disponível para os servidores de fins gerais e memória otimizada.

> [!NOTE]
> A funcionalidade de proteção avançada contra ameaças está **não** disponíveis nas regiões de cloud soberana e seguir o Azure government: US Gov Texas, EUA Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Alemanha Central, Alemanha norte, leste da China, leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral do produto.

## <a name="what-is-advanced-threat-protection"></a>O que é a proteção avançada contra ameaças?

Proteção avançada contra ameaças para a base de dados do Azure para PostgreSQL fornece uma nova camada de segurança, o que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem ao fornecer alertas de segurança relativamente a atividades anómalas. Os utilizadores recebem um alerta após a atividades suspeitas da base de dados e potenciais vulnerabilidades, bem como os padrões de acesso e consultas de base de dados anómalas. Proteção avançada contra ameaças para a base de dados do Azure para PostgreSQL integra alertas no [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividade suspeita e recomenda ação sobre como investigar e mitigar a ameaça. Proteção avançada contra ameaças para a base de dados do Azure para PostgreSQL facilita lidar com potenciais ameaças à base de dados sem a necessidade de ser um especialista em segurança ou gerir sistemas de monitorização de segurança avançada. 

![Conceito de proteção avançada contra ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas de proteção contra ameaças avançadas 
Proteção avançada contra ameaças para a base de dados do Azure para PostgreSQL Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de bases de dados e ele pode acionar os seguintes alertas:
- **Acesso a partir de uma localização invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso à base de dados do Azure para o servidor PostgreSQL, em que alguém iniciou sessão para a base de dados do Azure para o servidor PostgreSQL a partir de uma localização geográfica invulgar. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir do Centro de dados do Azure invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso à base de dados do Azure para o servidor PostgreSQL, em que alguém iniciou sessão para o servidor de um centro de dados do Azure invulgar que foi visto neste servidor durante o período recente. Em alguns casos, o alerta Deteta uma ação legítima (sua nova aplicação no Azure, Power BI, base de dados para o Editor de consultas do PostgreSQL). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso a partir de principal invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso à base de dados do Azure para o servidor PostgreSQL, em que alguém iniciou sessão no servidor com um principal invulgar (base de dados Azure para PostgreSQL utilizador). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir de uma aplicação potencialmente prejudicial**: Este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder a base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Base de dados do Azure de força bruta para credenciais de PostgreSQL**: Este alerta é acionado quando existe um número anormalmente elevado de inícios de sessão falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre os preços, consulte o [base de dados do Azure para a página de preços do PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) 
* Configurar [base de dados do Azure para PostgreSQL Advanced Threat Protection](howto-database-threat-protection-portal.md) no portal do Azure  
