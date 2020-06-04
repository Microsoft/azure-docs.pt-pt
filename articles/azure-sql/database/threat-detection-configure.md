---
title: Configurar o Advanced Threat Protection
description: A Advanced Threat Protection deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84321551"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configure proteção avançada de ameaças para base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[A Advanced Threat Protection](threat-detection-overview.md) for Azure SQL Database deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados. A Advanced Threat Protection pode identificar **a injeção potencial de SQL**, acesso a partir de **localização incomum ou centro de dados**, **Acesso a aplicações principais ou potencialmente nocivas ,** e **credenciais de SQL da força bruta** - ver mais detalhes em [alertas de Proteção de Ameaças Avançadas](threat-detection-overview.md#alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou [portal Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[A Advanced Threat Protection](threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados,](advanced-data-security.md) que é um pacote unificado para capacidades avançadas de segurança SQL. A Proteção Avançada de Ameaças pode ser acedida e gerida através do portal central de Segurança de Dados Avançados sql.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Criar proteção avançada de ameaças no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a página de configuração do servidor que pretende proteger. Nas definições de segurança, selecione **Advanced Data Security**.
3. Na página de configuração **de Segurança de Dados Avançados:**

   - Ativar a Segurança Avançada de Dados no servidor.
   - Nas **Definições avançadas de proteção contra ameaças,** nos **alertas enviar para a** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades de base de dados anómalas.
  
   ![Configurar proteção avançada de ameaças](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar o Advanced Threat Protection com o PowerShell

Para um exemplo de script, consulte [a auditoria de Configuração e a Proteção avançada de ameaças utilizando o PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a Proteção Avançada de Ameaças.](threat-detection-overview.md)
- Saiba mais sobre [a Proteção avançada de ameaças em SQL Casos Geridos](../managed-instance/threat-detection-configure.md).  
- Saiba mais sobre [segurança avançada de dados.](advanced-data-security.md)
- Saiba mais sobre [auditoria](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre [o Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
