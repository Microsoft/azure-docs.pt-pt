---
title: Proteção avançada contra ameaças-banco de dados do Azure para MariaDB | Microsoft Docs
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a96d8c3b9f9e3f9b1aedf269bce141da3e4ccf42
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869693"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Banco de dados do Azure para proteção avançada contra ameaças do MariaDB

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

> [!IMPORTANT]
> A proteção avançada contra ameaças está em visualização pública.

A proteção avançada contra ameaças faz parte da oferta de segurança de dados avançada, que é um pacote unificado para recursos avançados de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com). O recurso está disponível para servidores Uso Geral e com otimização de memória.

> [!NOTE]
> O recurso proteção avançada contra ameaças **não** está disponível nas seguintes regiões do Azure governamental e do soberanas Cloud: US Gov Texas, US Gov Arizona, US Gov Iowa, US, gov Virgínia, US DoD Leste, US DoD Central, Alemanha central, Norte da Alemanha, Leste da China, Leste da China 2. Visite os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral do produto.


## <a name="what-is-advanced-threat-protection"></a>O que é a proteção avançada contra ameaças?

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários recebem um alerta sobre atividades suspeitas de banco de dados e possíveis vulnerabilidades, bem como padrões de consultas e acessos de banco de dados anormais. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB simplifica a endereçamento de ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados. 

![Conceito de proteção avançada contra ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas de proteção avançada contra ameaças 
A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de local incomum**: Esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no banco de dados do Azure para o servidor MariaDB de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de data center incomuns do Azure**: Esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no servidor de um data center do Azure incomum que foi visto nesse servidor durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, Power BI). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso de entidade de segurança não familiar**: Esse alerta é disparado quando há uma alteração no padrão de acesso para o banco de dados do Azure para MariaDB Server, em que alguém fez logon no servidor usando uma entidade de segurança incomum (banco de dados do Azure para usuário MariaDB). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de um aplicativo potencialmente prejudicial**: Esse alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Banco de dados do Azure de força bruta para credenciais do MariaDB**: Esse alerta é disparado quando há um grande número anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configurar o [banco de dados do Azure para proteção avançada contra ameaças do MariaDB](howto-database-threat-protection-portal.md) usando o portal do Azure  
