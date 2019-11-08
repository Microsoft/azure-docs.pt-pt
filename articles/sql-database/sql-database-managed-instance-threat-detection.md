---
title: Configurar a proteção avançada contra ameaças – instância gerenciada
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados em uma instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822555"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Configurar a proteção avançada contra ameaças na instância gerenciada do banco de dados SQL do Azure

A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) para uma [instância gerenciada](sql-database-managed-instance-index.yml) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A proteção avançada contra ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso a partir de um aplicativo não familiar ou potencialmente prejudicial**e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de proteção avançada contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) faz parte da oferta do ADS ( [segurança de dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurar a proteção avançada contra ameaças no portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração da instância gerenciada que você deseja proteger. Na página **configurações** , selecione **segurança de dados avançada**.
3. Na página configuração de segurança de dados avançada
   - Ative a segurança **de** dados avançada.
   - Configure a **lista de emails** para receber alertas de segurança após a detecção de atividades anormais do banco de dados.
   - Selecione a **conta de armazenamento do Azure** em que os registros de auditoria de ameaças Anômalas são salvos.
   - Selecione os **tipos de proteção avançada contra ameaças** que você gostaria de configurar. Saiba mais sobre [alertas de proteção avançada contra ameaças](sql-database-threat-detection-overview.md).
4. Clique em **salvar** para salvar a política de segurança de dados avançada nova ou atualizada.

   ![Proteção Avançada Contra Ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Os preços nas capturas de tela nem sempre refletem o preço atual e são um exemplo.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [proteção avançada contra ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre instâncias gerenciadas, consulte [o que é uma instância gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [a proteção avançada contra ameaças para um banco de dados individual](sql-database-threat-detection.md).
- Saiba mais sobre a [auditoria de instância gerenciada](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
