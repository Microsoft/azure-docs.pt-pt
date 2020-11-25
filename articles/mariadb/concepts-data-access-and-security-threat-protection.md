---
title: Proteção Avançada de Ameaças - Base de Dados Azure para MariaDB
description: A Advanced Threat Protection deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95977031"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Base de Dados Azure para a Proteção Avançada de Ameaças MariaDB

A Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

> [!IMPORTANT]
> A Proteção Avançada de Ameaças está em visualização pública.

A Advanced Threat Protection faz parte da oferta avançada de Segurança de Dados, que é um pacote unificado para capacidades avançadas de segurança. A Advanced Threat Protection pode ser acedida e gerida através do [portal Azure](https://portal.azure.com). A funcionalidade está disponível para servidores otimizados para fins gerais e memória.

> [!NOTE]
> A funcionalidade de Proteção de Ameaças **Avançadas não** está disponível nas seguintes regiões de nuvens governamentais e soberanas: EUA Gov Texas, EUA Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral de produtos.


## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada de Ameaças?

A Advanced Threat Protection for Azure Database for MariaDB fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de base de dados, e potenciais vulnerabilidades, bem como padrões anómalos de acesso e consultas de bases de dados. A Advanced Threat Protection for Azure Database for MariaDB integra alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e mitigar a ameaça. A Proteção Avançada de Ameaças para a Base de Dados de Azure para o MariaDB torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança. 

![Conceito avançado de proteção de ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas avançados de proteção contra ameaças 
A Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados e pode desencadear os seguintes alertas:
- **Acesso a partir de localização invulgar**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MariaDB, onde alguém iniciou sessão na Base de Dados Azure para o servidor MariaDB a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir de um centro de dados Azure incomum**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MariaDB, onde alguém iniciou sessão no servidor a partir de um centro de dados Azure incomum que foi visto neste servidor durante o período recente. Em alguns casos, o alerta deteta uma ação legítima (a sua nova aplicação em Azure, Power BI). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso a partir de um principal desconhecido**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MariaDB, onde alguém iniciou sessão no servidor utilizando um principal incomum (Base de Dados Azure para utilizador MariaDB). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Base de dados Azure força bruta para credenciais MariaDB**: Este alerta é acionado quando há um número anormal elevado de logins falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Security Center](../security-center/security-center-introduction.md)
* Para obter mais informações sobre preços, consulte a [Base de Dados Azure para a página de preços da MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configure [Azure Database for MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) using the Azure portal