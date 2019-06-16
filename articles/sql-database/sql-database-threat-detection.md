---
title: Deteção de ameaças - base de dados SQL do Azure | Documentos da Microsoft
description: Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados num único banco de dados ou conjunto elástico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331493"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Deteção de ameaças de base de dados SQL do Azure para as bases de dados individuais ou agrupadas

[Deteção de ameaças](sql-database-threat-detection-overview.md) para bases de dados individuais e em pool Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de bases de dados. Deteção de ameaças pode identificar **injeção SQL potencial**, **acesso a partir do Centro de dados ou localização invulgar**, **acesso a partir de familiarizado aplicativo de principal ou potencialmente prejudicial**, e **credenciais SQL de força bruta** -veja mais detalhes no [alertas de deteção de ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Deteção de ameaças](sql-database-threat-detection-overview.md) faz parte dos [dados de segurança avançada](sql-database-advanced-data-security.md) (ADS) oferta, que é um pacote unificado para funções de segurança avançadas do SQL. Deteção de ameaças pode ser acessada e gerenciada através do portal de anúncios de SQL central. O pacote de segurança avançada de dados é cobrado 15$ / mês por servidor lógico, com os primeiros 30 dias sem encargos.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurar a deteção de ameaças da base de dados no portal do Azure

1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com).
2. Navegue para a página de configuração do servidor SQL Database do Azure que pretende proteger. Nas definições de segurança, selecione **segurança de dados avançada**.
3. Sobre o **segurança de dados avançada** página de configuração:

   - Ative a segurança de dados avançada no servidor.
   - Na **as definições de deteção de ameaças**, na **enviar alertas para** texto caixa, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades anómalas da base de dados.
  
   ![Configurar a deteção de ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configurar a deteção de ameaças através do PowerShell

Para obter um exemplo de script, consulte [configurar a deteção de ameaças e auditoria com o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [deteção de ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre [deteção de ameaças na instância gerida](sql-database-managed-instance-threat-detection.md).  
- Saiba mais sobre [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre [auditoria](sql-database-auditing.md)
- Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre os preços, consulte o [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/)  
