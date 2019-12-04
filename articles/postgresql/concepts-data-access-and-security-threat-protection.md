---
title: Proteção avançada contra ameaças-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como usar a proteção avançada contra ameaças para detectar atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3d86c76472580567c95d285924761e1714465d6f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768746"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Proteção avançada contra ameaças no banco de dados do Azure para PostgreSQL-servidor único

A Proteção Avançada Contra Ameaças para Base de Dados do Azure para PostgreSQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares para aceder ou explorar as suas bases de dados.

> [!NOTE]
> A proteção avançada contra ameaças está em visualização pública.

A proteção contra ameaças faz parte da oferta de proteção de ameaças avançada (ATP), que é um pacote unificado para recursos avançados de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com) ou usando a [API REST](/rest/api/postgresql/serversecurityalertpolicies). O recurso está disponível para servidores Uso Geral e com otimização de memória.

> [!NOTE]
> O recurso proteção avançada contra ameaças **não** está disponível nas seguintes regiões do Azure governamental e do soberanas cloud: US gov Texas, US gov Arizona, US gov Iowa, US, gov virgínia, US DoD Leste, US DoD central, Alemanha Central, Norte da Alemanha, Leste da China, leste da China 2. Visite os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral do produto.

## <a name="what-is-advanced-threat-protection"></a>O que é a proteção avançada contra ameaças?

A proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários recebem um alerta sobre atividades suspeitas de banco de dados e possíveis vulnerabilidades, bem como padrões de consultas e acessos de banco de dados anormais. A proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL torna simples lidar com ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados. 

![Conceito de proteção avançada contra ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas de proteção avançada contra ameaças 
A proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de local incomum**: esse alerta é disparado quando há uma alteração no padrão de acesso para o servidor do banco de dados do Azure para PostgreSQL, em que alguém fez logon no banco de dados do Azure para o servidor PostgreSQL de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de data center incomuns do Azure**: esse alerta é disparado quando há uma alteração no padrão de acesso para o servidor do banco de dados do Azure para PostgreSQL, em que alguém fez logon no servidor de um data center do Azure incomum que foi visto nesse servidor durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, Power BI, editor de consultas do banco de dados do Azure para PostgreSQL). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso a partir de uma entidade de segurança desconhecida**: esse alerta é disparado quando há uma alteração no padrão de acesso para o servidor do banco de dados do Azure para PostgreSQL, em que alguém fez logon no servidor usando uma entidade de segurança incomum (banco de dados do Azure para o usuário PostgreSQL). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Força bruta do banco de dados do Azure para PostgreSQL credenciais**: esse alerta é disparado quando há um grande número anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) 
* Configurar a [proteção avançada contra ameaças do banco de dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md) usando o portal do Azure  
