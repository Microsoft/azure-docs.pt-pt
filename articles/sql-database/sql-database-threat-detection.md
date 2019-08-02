---
title: Detecção de ameaças-banco de dados SQL do Azure | Microsoft Docs
description: A detecção de ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados em um único banco de dados ou pool elástico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 5549d016978e8bf9491c3745e335e3c4c793212c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566322"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Detecção de ameaças do banco de dados SQL do Azure para bancos de dados individuais ou em pool

A [detecção de ameaças](sql-database-threat-detection-overview.md) para bancos de dados individuais e em pool detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A detecção de ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso de aplicativo de entidade de segurança ou aplicativos potencialmente prejudiciais**desconhecidos e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de detecção de ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

A [detecção de ameaças](sql-database-threat-detection-overview.md) faz parte da oferta do ADS (segurança de [dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A detecção de ameaças pode ser acessada e gerenciada por meio do portal de anúncios do SQL central. O pacote de segurança de dados avançado é cobrado 15 $/mês por servidor lógico, com os primeiros 30 dias gratuitamente.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurar a detecção de ameaças para seu banco de dados no portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração do servidor de banco de dados SQL do Azure que você deseja proteger. Em configurações de segurança, selecione **segurança de dados avançada**.
3. Na página configuração de **segurança de dados avançada** :

   - Habilite a segurança de dados avançada no servidor.
   - Em **configurações de detecção de ameaças**, na caixa de texto **enviar alertas para** , forneça a lista de emails para receber alertas de segurança após a detecção de atividades anormais de banco de dados.
  
   ![Configurar a detecção de ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configurar a detecção de ameaças usando o PowerShell

Para obter um exemplo de script, consulte [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a [detecção de ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre a [detecção de ameaças na instância gerenciada](sql-database-managed-instance-threat-detection.md).  
- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre a [auditoria](sql-database-auditing.md)
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
