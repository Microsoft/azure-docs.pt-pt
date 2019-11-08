---
title: Configurar a proteção avançada contra ameaças
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados em um único banco de dados ou pool elástico.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822496"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Proteção avançada contra ameaças do banco de dados SQL do Azure para bancos de dados individuais ou em pool

A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) para bancos de dados individuais e em pool detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A proteção avançada contra ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso a partir de um aplicativo não familiar ou potencialmente prejudicial**e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de proteção avançada contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) faz parte da oferta do ADS ( [segurança de dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurar a proteção avançada contra ameaças no portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração do servidor de banco de dados SQL do Azure que você deseja proteger. Em configurações de segurança, selecione **segurança de dados avançada**.
3. Na página configuração de **segurança de dados avançada** :

   - Habilite a segurança de dados avançada no servidor.
   - Em **Configurações avançadas de proteção contra ameaças**, na caixa de texto **enviar alertas para** , forneça a lista de emails para receber alertas de segurança após a detecção de atividades anormais do banco de dados.
  
   ![Configurar a proteção avançada contra ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Os preços nas capturas de tela nem sempre refletem o preço atual e são um exemplo.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar a proteção avançada contra ameaças usando o PowerShell

Para obter um exemplo de script, consulte [Configurar a auditoria e a proteção avançada contra ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [proteção avançada contra ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre a [proteção avançada contra ameaças na instância gerenciada](sql-database-managed-instance-threat-detection.md).  
- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre a [auditoria](sql-database-auditing.md)
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
