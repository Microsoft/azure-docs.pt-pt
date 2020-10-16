---
title: Proteção Avançada de Ameaças - Base de Dados Azure para o MySQL
description: Saiba conceitos sobre a Advanced Threat Protection, que deteta atividades anómalas de bases de dados que indiquem potenciais ameaças de segurança à base de dados.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ccee3b52b0f204561ca790ce364308f81a1db6f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906594"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Base de Dados Azure para a Proteção Avançada de Ameaças MySQL

A Advanced Threat Protection para Base de Dados do Azure para MySQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares para aceder ou explorar as suas bases de dados.

> [!NOTE]
> A Proteção Avançada de Ameaças está em visualização pública.

A Advanced Threat Protection faz parte da oferta avançada de Segurança de Dados, que é um pacote unificado para capacidades avançadas de segurança. A Advanced Threat Protection pode ser acedida e gerida através do [portal Azure](https://portal.azure.com) ou utilizando [a API REST](/rest/api/mysql). A funcionalidade está disponível para servidores otimizados para fins gerais e memória.

> [!NOTE]
> A funcionalidade de Proteção de Ameaças **Avançadas não** está disponível nas seguintes regiões de nuvens governamentais e soberanas: EUA Gov Texas, EUA Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral de produtos.


## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada de Ameaças?

A Advanced Threat Protection for Azure Database for MySQL fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de base de dados, e potenciais vulnerabilidades, bem como padrões anómalos de acesso e consultas de bases de dados. A Advanced Threat Protection for Azure Database for MySQL integra alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e mitigar a ameaça. A Proteção Avançada de Ameaças para a Base de Dados de Azure para o MySQL torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Conceito avançado de proteção de ameaças":::

## <a name="advanced-threat-protection-alerts"></a>Alertas avançados de proteção contra ameaças 
A Advanced Threat Protection for Azure Database for MySQL deteta atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados e pode desencadear os seguintes alertas:
- **Acesso a partir de localização invulgar**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MySQL, onde alguém iniciou sessão na Base de Dados Azure para o servidor MySQL a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir de um centro de dados Azure incomum**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MySQL, onde alguém iniciou sessão no servidor a partir de um centro de dados Azure incomum que foi visto neste servidor durante o período recente. Em alguns casos, o alerta deteta uma ação legítima (a sua nova aplicação em Azure, Power BI, Azure Database for MySQL Query Editor). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso a partir de um principal desconhecido**: Este alerta é desencadeado quando há uma alteração no padrão de acesso à Base de Dados Azure para o servidor MySQL, onde alguém iniciou sessão no servidor utilizando um principal incomum (Base de Dados Azure para utilizador mySQL). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Base de dados Azure força bruta para credenciais MySQL**: Este alerta é acionado quando há um número anormal elevado de logins falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [Base de Dados Azure para a página de preços mySQL](https://azure.microsoft.com/pricing/details/mysql/) 
* Configure [Base de Dados Azure para a Proteção avançada de ameaças do MySQL](howto-database-threat-protection-portal.md) utilizando o portal Azure  
