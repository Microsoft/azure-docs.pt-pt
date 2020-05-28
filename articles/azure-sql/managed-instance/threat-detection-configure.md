---
title: Configurar o Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: A Advanced Threat Protection deteta atividades anómalas de base de dados que indicam potenciais ameaças à segurança na base de dados em 1º Caso Gerido por Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: a60156762a4d8dfb6b11ae70e608fb26b07e5764
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045761"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Configurar proteção avançada de ameaças em instância gerida azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[A Proteção avançada de ameaças](../database/threat-detection-overview.md) para um [Caso Gerido Azure SQL](sql-managed-instance-paas-overview.md) deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. A Proteção avançada de Ameaças pode identificar **a injeção de Potencial SQL,** acesso a partir de localização ou centro de dados **incomuns,** acesso **a aplicação principal desconhecida ou potencialmente prejudicial**, e **credenciais SQL de força bruta** - ver mais detalhes em alertas avançados de [proteção contra ameaças](../database/threat-detection-overview.md#alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) de email ou [portal Azure](../database/threat-detection-overview.md#explore-alerts-in-azure-portal)

[Advanced Threat Protection](../database/threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados,](../database/advanced-data-security.md) que é um pacote unificado para capacidades avançadas de segurança SQL. O Advanced Threat Protection pode ser acedido e gerido através do portal ADS SQL central.

##  <a name="azure-portal"></a>Portal do Azure

1. Assine no [portal Azure.](https://portal.azure.com) 
2. Navegue para a página de configuração do Caso Gerido SQL que pretende proteger. Na página **Definições,** selecione **Advanced Data Security**.
3. Na página de configuração avançada de segurança de dados
   - LIGUE **ON** A Segurança Avançada de Dados.
   - Configure a **lista de e-mails** para receber alertas de segurança após a deteção de atividades anómalas na base de dados.
   - Selecione a conta de **armazenamento Azure** onde os registos de auditoria de ameaças anómalas são guardados.
   - Selecione os tipos avançados de **proteção contra ameaças** que gostaria de configurar. Saiba mais sobre alertas avançados de [proteção contra ameaças.](../database/threat-detection-overview.md)
4. Clique em **Guardar** para salvar a nova ou atualizada política de Segurança de Dados Avançados.

   ![Proteção Avançada Contra Ameaças](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a Proteção avançada de Ameaças.](../database/threat-detection-overview.md)
- Aprenda sobre casos geridos, veja [O que é uma instância gerida Azure SQL](sql-managed-instance-paas-overview.md).
- Saiba mais sobre a Proteção avançada de ameaças para a Base de [Dados SQL Azure](../database/threat-detection-configure.md).
- Saiba mais sobre a auditoria de Instância Gerida pela [SQL](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre o [centro de segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)
