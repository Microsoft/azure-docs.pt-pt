---
title: Configurar Proteção avançada de ameaças - instância gerida
description: A Advanced Threat Protection deteta atividades anómalas de base de dados que indicam potenciais ameaças à segurança na base de dados numa instância gerida.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822555"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Configure avançado proteção contra ameaças em caso de base de dados Azure SQL gerido

[A Proteção avançada contra ameaças](sql-database-threat-detection-overview.md) para um [caso gerido](sql-database-managed-instance-index.yml) deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. A Proteção avançada de Ameaças pode identificar **a injeção de Potencial SQL,** acesso a partir de localização ou centro de dados **incomuns,** acesso **a aplicação principal desconhecida ou potencialmente prejudicial**, e **credenciais SQL de força bruta** - ver mais detalhes em alertas avançados de [proteção contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) de email ou [portal Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para capacidades avançadas de segurança SQL. O Advanced Threat Protection pode ser acedido e gerido através do portal ADS SQL central.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Criação de Proteção Avançada de Ameaças no portal Azure

1. Lance o portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. Navegue para a página de configuração da instância gerida que pretende proteger. Na página **Definições,** selecione **Advanced Data Security**.
3. Na página de configuração avançada de segurança de dados
   - LIGUE **ON** A Segurança Avançada de Dados.
   - Configure a **lista de e-mails** para receber alertas de segurança após a deteção de atividades anómalas na base de dados.
   - Selecione a conta de **armazenamento Azure** onde os registos de auditoria de ameaças anómalas são guardados.
   - Selecione os tipos avançados de **proteção contra ameaças** que gostaria de configurar. Saiba mais sobre alertas avançados de [proteção contra ameaças.](sql-database-threat-detection-overview.md)
4. Clique em **Guardar** para salvar a nova ou atualizada política de Segurança de Dados Avançados.

   ![Proteção Avançada Contra Ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Os preços nas imagens nem sempre refletem o preço atual, e são um exemplo.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Proteção avançada de Ameaças.](sql-database-threat-detection-overview.md)
- Aprenda sobre casos geridos, veja [O que é um caso gerido](sql-database-managed-instance.md).
- Saiba mais sobre [a Proteção avançada de ameaças para uma única base de dados](sql-database-threat-detection.md).
- Saiba mais sobre auditoria de [exemplos geridos.](https://go.microsoft.com/fwlink/?linkid=869430)
- Saiba mais sobre o [centro de segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)
