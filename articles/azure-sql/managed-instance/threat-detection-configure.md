---
title: Configurar o Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: A Advanced Threat Protection deteta atividades de base de dados anómalas que indiquem potenciais ameaças de segurança à base de dados em Azure SQL Managed Instance.
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
ms.openlocfilehash: cfd0aa6cf62c4f70365ecc06ad75efdda9563470
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310823"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Configure proteção avançada de ameaças em Azure SQL Caso gerido
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[A Advanced Threat Protection](../database/threat-detection-overview.md) for [a Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) deteta atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados. A Advanced Threat Protection pode identificar **a injeção potencial de SQL**, acesso a partir de **localização incomum ou centro de dados**, **Acesso a aplicações principais ou potencialmente nocivas ,** e **credenciais de SQL da força bruta** - ver mais detalhes em [alertas de Proteção de Ameaças Avançadas](../database/threat-detection-overview.md#alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou [portal Azure](../database/threat-detection-overview.md#explore-alerts-in-azure-portal)

[A Advanced Threat Protection](../database/threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados,](../database/advanced-data-security.md) que é um pacote unificado para capacidades avançadas de segurança SQL. O Advanced Threat Protection pode ser acedido e gerido através do portal ADS SQL central.

##  <a name="azure-portal"></a>Portal do Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com). 
2. Navegue para a página de configuração da instância de SQL Managed Instance que pretende proteger. Na página **Definições,** selecione **Advanced Data Security**.
3. Na página de configuração de Segurança de Dados Avançados
   - Ligue **a** segurança de dados avançados.
   - Configure a **lista de e-mails** para receber alertas de segurança após a deteção de atividades de base de dados anómalas.
   - Selecione a **conta de armazenamento Azure** onde são guardados registos de auditoria de ameaças anómalas.
   - Selecione os **tipos de Proteção de Ameaças Avançadas** que gostaria de configurar. Saiba mais sobre [alertas avançados de proteção contra ameaças.](../database/threat-detection-overview.md)
4. Clique em **Guardar** para guardar a nova ou atualizada política de Segurança avançada de dados.

   ![Advanced Threat Protection](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Proteção Avançada de Ameaças.](../database/threat-detection-overview.md)
- Conheça as instâncias geridas, veja [O que é um Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Saiba mais sobre [a Advanced Threat Protection for Azure SQL Database](../database/threat-detection-configure.md).
- Saiba mais sobre [a auditoria da SQL Managed Instance](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre [o Centro de Segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)
