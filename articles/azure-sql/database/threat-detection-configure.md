---
title: Configurar o Advanced Threat Protection
description: A Advanced Threat Protection deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: 68ab5c820f3a67a7fd332557d47918d2a7aa4b62
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789425"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configure proteção avançada de ameaças para base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[A Advanced Threat Protection](threat-detection-overview.md) for Azure SQL Database deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados. A Advanced Threat Protection pode identificar **a injeção potencial de SQL** , acesso a partir de **localização incomum ou centro de dados** , **Acesso a aplicações principais ou potencialmente nocivas ,** e **credenciais de SQL da força bruta** - ver mais detalhes em [alertas de Proteção de Ameaças Avançadas](threat-detection-overview.md#alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou [portal Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](threat-detection-overview.md) faz parte da oferta [Azure Defender for SQL,](azure-defender-for-sql.md) que é um pacote unificado para capacidades avançadas de segurança SQL. A Advanced Threat Protection pode ser acedida e gerida através do Azure Defender central para o portal SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Criar proteção avançada de ameaças no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a página de configuração do servidor que pretende proteger. Nas definições de segurança, selecione **Centro de Segurança** .
3. Na página de configuração do **Azure Defender:**

   - Ativar o Azure Defender no servidor.
   - Nas **Definições avançadas de proteção contra ameaças,** nos **alertas enviar para a** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades de base de dados anómalas.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar o Advanced Threat Protection com o PowerShell

Para um exemplo de script, consulte [a auditoria de Configuração e a Proteção avançada de ameaças utilizando o PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Proteção Avançada de Ameaças.](threat-detection-overview.md)
- Saiba mais sobre [a Proteção avançada de ameaças em SQL Casos Geridos](../managed-instance/threat-detection-configure.md).  
- Saiba mais sobre [o Azure Defender para a SQL](azure-defender-for-sql.md).
- Saiba mais sobre [auditoria](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre [o Centro de Segurança Azure](../../security-center/security-center-introduction.md)
- Para obter mais informações sobre preços, consulte a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)