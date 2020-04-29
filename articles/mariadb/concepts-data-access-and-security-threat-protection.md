---
title: Proteção avançada de ameaças - Base de Dados Azure para MariaDB
description: A Advanced Threat Protection deteta atividades anómalas na base de dados que indicam potenciais ameaças à segurança na base de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532183"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Base de Dados Azure para Proteção Avançada de Ameaças MariaDB

Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

> [!IMPORTANT]
> A Proteção Avançada de Ameaças está em pré-visualização pública.

A Advanced Threat Protection faz parte da oferta avançada de segurança de dados, que é um pacote unificado para capacidades de segurança avançadas. A Proteção Avançada de Ameaças pode ser acedida e gerida através do [portal Azure.](https://portal.azure.com) A funcionalidade está disponível para servidores otimizados para Fins Gerais e Memória.

> [!NOTE]
> A funcionalidade de Proteção contra Ameaças **Avançadas não** está disponível nas seguintes regiões do governo azure e das nuvens soberanas: EUA Gov Texas, US Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Alemanha Central, Alemanha Norte, China Leste, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para a disponibilidade geral do produto.


## <a name="what-is-advanced-threat-protection"></a>O que é Proteção avançada de ameaças?

A Advanced Threat Protection for Azure Database for MariaDB fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de bases de dados e potenciais vulnerabilidades, bem como padrões anómalos de acesso à base de dados e consultas. Advanced Threat Protection for Azure Database for MariaDB integra alertas com o [Azure Security Center](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e mitigar a ameaça. A Advanced Threat Protection for Azure Database for MariaDB torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança. 

![Conceito avançado de proteção contra ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas avançados de proteção contra ameaças 
Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados e pode desencadear os seguintes alertas:
- **Acesso**a partir de uma localização invulgar : Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para servidor MariaDB, onde alguém acedeu à Base de Dados Azure para servidor MariaDB a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir de um centro de dados incomum do Azure**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MariaDB, onde alguém acedeu ao servidor a partir de um centro de dados incomum do Azure que foi visto neste servidor durante o período recente. Em alguns casos, o alerta deteta uma ação legítima (a sua nova aplicação em Azure, Power BI). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso do principal desconhecido**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para servidor MariaDB, onde alguém acedeu ao servidor usando um principal incomum (Base de Dados Azure para utilizador MariaDB). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Brute force Azure Database para credenciais MariaDB**: Este alerta é desencadeado quando há um número anormal de logins falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para mais informações sobre preços, consulte a Base de [Dados Azure para a página de Preços MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configure [Azure Database para Proteção avançada de ameaças MariaDB](howto-database-threat-protection-portal.md) usando o portal Azure  
