---
title: Proteção avançada de ameaças - Base de dados Azure para PostgreSQL - Servidor Único
description: Saiba usar a Proteção avançada de ameaças para detetar atividades anómalas na base de dados que indicam potenciais ameaças à segurança na base de dados.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3d86c76472580567c95d285924761e1714465d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768746"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Proteção avançada de ameaças na base de dados Azure para PostgreSQL - Servidor Único

O Advanced Threat Protection da Base de Dados do Azure para PostgreSQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares de acesso ou exploração das bases de dados.

> [!NOTE]
> A Proteção Avançada de Ameaças está em pré-visualização pública.

A Proteção contra ameaças faz parte da oferta de Proteção avançada de Ameaças (ATP), que é um pacote unificado para capacidades de segurança avançadas. A Proteção Avançada de Ameaças pode ser acedida e gerida através do [portal Azure](https://portal.azure.com) ou utilizando a [Rest API](/rest/api/postgresql/serversecurityalertpolicies). A funcionalidade está disponível para servidores otimizados para Fins Gerais e Memória.

> [!NOTE]
> A funcionalidade de Proteção contra Ameaças **Avançadas não** está disponível nas seguintes regiões do governo azure e das nuvens soberanas: EUA Gov Texas, US Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Alemanha Central, Alemanha Norte, China Leste, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para a disponibilidade geral do produto.

## <a name="what-is-advanced-threat-protection"></a>O que é Proteção avançada de ameaças?

A Advanced Threat Protection for Azure Database for PostgreSQL fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de bases de dados e potenciais vulnerabilidades, bem como padrões anómalos de acesso à base de dados e consultas. Advanced Threat Protection for Azure Database for PostgreSQL integra alertas com o [Azure Security Center](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e mitigar a ameaça. A Advanced Threat Protection for Azure Database for PostgreSQL torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança. 

![Conceito avançado de proteção contra ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas avançados de proteção contra ameaças 
A Advanced Threat Protection for Azure Database for PostgreSQL deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados e pode desencadear os seguintes alertas:
- **Acesso**a partir de uma localização invulgar : Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para servidor PostgreSQL, onde alguém acedeu à Base de Dados Azure para servidor PostgreSQL a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir de um centro**de dados incomum do Azure : Este alerta é desencadeado quando há uma alteração no padrão de acesso ao servidor Azure Database para o servidor PostgreSQL, onde alguém acedeu ao servidor a partir de um invulgar centro de dados Azure que foi visto neste servidor durante o período recente. Em alguns casos, o alerta deteta uma ação legítima (a sua nova aplicação em Azure, Power BI, Azure Database para PostgreSQL Query Editor). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso a partir de um principal desconhecido**: Este alerta é desencadeado quando há uma alteração no padrão de acesso ao servidor Azure Database para o servidor PostgreSQL, onde alguém acedeu ao servidor utilizando um principal incomum (Base de Dados Azure para utilizador PostgreSQL). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Brute force Azure Database para credenciais PostgreSQL**: Este alerta é desencadeado quando há um número anormal de logins falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a Base de [Dados Azure para a página de preços PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) 
* Configure [Base de Dados Azure para Proteção avançada de ameaças postgresQL](howto-database-threat-protection-portal.md) usando o portal Azure  
