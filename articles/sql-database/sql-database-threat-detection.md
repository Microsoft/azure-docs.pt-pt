---
title: Configurar o Advanced Threat Protection
description: A Advanced Threat Protection deteta atividades anómalas de base de dados que indicam potenciais ameaças à segurança na base de dados numa única base de dados ou num pool elástico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822496"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Base de Dados Azure SQL Proteção avançada de ameaças para bases de dados individuais ou reunidas

[A Proteção avançada de ameaças](sql-database-threat-detection-overview.md) para bases de dados individuais e reunidas deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. A Proteção avançada de Ameaças pode identificar **a injeção de Potencial SQL,** acesso a partir de localização ou centro de dados **incomuns,** acesso **a aplicação principal desconhecida ou potencialmente prejudicial**, e **credenciais SQL de força bruta** - ver mais detalhes em alertas avançados de [proteção contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) de email ou [portal Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para capacidades avançadas de segurança SQL. O Advanced Threat Protection pode ser acedido e gerido através do portal ADS SQL central.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Criação de Proteção Avançada de Ameaças no portal Azure

1. Lance o portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. Navegue para a página de configuração do servidor de base de dados Azure SQL que pretende proteger. Nas definições de segurança, selecione **Advanced Data Security**.
3. Na página de configuração avançada de **segurança de dados:**

   - Ativar a Segurança avançada de dados no servidor.
   - Em **Definições Avançadas de Proteção contra Ameaças,** nos **alertas enviar para** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades anómalas na base de dados.
  
   ![Criar proteção avançada de ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Os preços nas imagens nem sempre refletem o preço atual, e são um exemplo.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar o Advanced Threat Protection com o PowerShell

Para um exemplo de script, consulte [a auditoria do Configure e a Proteção avançada de ameaças utilizando powerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Proteção avançada de Ameaças.](sql-database-threat-detection-overview.md)
- Saiba mais sobre [a Proteção avançada de ameaças em caso gerido](sql-database-managed-instance-threat-detection.md).  
- Saiba mais sobre [a segurança avançada de dados.](sql-database-advanced-data-security.md)
- Saiba mais sobre [auditoria](sql-database-auditing.md)
- Saiba mais sobre o [centro de segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre os preços, consulte a página de preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
