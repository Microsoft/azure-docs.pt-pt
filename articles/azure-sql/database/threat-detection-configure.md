---
title: Configurar o Advanced Threat Protection
description: Advanced Threat Protection deteta atividades anómalas de base de dados que indicam potenciais ameaças à segurança na base de dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: e5af2a0b88ba46dd1c4751a8aeee4c118ca1b8ae
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049716"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configurar proteção avançada de ameaças para base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[A Advanced Threat Protection](threat-detection-overview.md) for Azure SQL Database deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. A Proteção avançada de Ameaças pode identificar **a injeção de Potencial SQL,** acesso a partir de localização ou centro de dados **incomuns,** acesso **a aplicação principal desconhecida ou potencialmente prejudicial**, e **credenciais SQL de força bruta** - ver mais detalhes em alertas avançados de [proteção contra ameaças](threat-detection-overview.md#alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações](threat-detection-overview.md#explore-detection-of-a-suspicious-event) de email ou [portal Azure](threat-detection-overview.md#explore-alerts-in-azure-portal)

[Advanced Threat Protection](threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados,](advanced-data-security.md) que é um pacote unificado para capacidades avançadas de segurança SQL. A Proteção Avançada de Ameaças pode ser acedida e gerida através do portal central de Segurança avançada de Dados SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Criação de Proteção Avançada de Ameaças no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a página de configuração do servidor que pretende proteger. Nas definições de segurança, selecione **Advanced Data Security**.
3. Na página de configuração avançada de **segurança de dados:**

   - Ativar a Segurança avançada de dados no servidor.
   - Em **Definições Avançadas de Proteção contra Ameaças,** nos **alertas enviar para** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades anómalas na base de dados.
  
   ![Criar proteção avançada de ameaças](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar o Advanced Threat Protection com o PowerShell

Para um exemplo de script, consulte [a auditoria do Configure e a Proteção avançada de ameaças utilizando powerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a Proteção avançada de Ameaças.](threat-detection-overview.md)
- Saiba mais sobre [a Proteção Avançada de Ameaças em Instância Gerida SQL](../managed-instance/threat-detection-configure.md).  
- Saiba mais sobre [a segurança avançada de dados.](advanced-data-security.md)
- Saiba mais sobre [auditoria](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre o [centro de segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre os preços, consulte a página de preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
