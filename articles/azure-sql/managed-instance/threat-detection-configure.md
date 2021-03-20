---
title: Configurar o Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: A Advanced Threat Protection deteta atividades de base de dados anómalas que indiquem potenciais ameaças de segurança à base de dados em Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 69bebcf872f55055117acf5cef410d1f89eafe34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446911"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Configure proteção avançada de ameaças em Azure SQL Caso gerido
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[A Advanced Threat Protection](../database/threat-detection-overview.md) for [a Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) deteta atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados. A Advanced Threat Protection pode identificar **a injeção potencial de SQL**, acesso a partir de **localização incomum ou centro de dados**, **Acesso a aplicações principais ou potencialmente nocivas ,** e **credenciais de SQL da força bruta** - ver mais detalhes em [alertas de Proteção de Ameaças Avançadas](../database/threat-detection-overview.md#alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou [portal Azure](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](../database/threat-detection-overview.md) faz parte da oferta [Azure Defender for SQL,](../database/azure-defender-for-sql.md)  que é um pacote unificado para capacidades avançadas de segurança SQL. A Advanced Threat Protection pode ser acedida e gerida através do Azure Defender central para o portal SQL.

##  <a name="azure-portal"></a>Portal do Azure

1. Inscreva-se no  [portal Azure](https://portal.azure.com). 
2. Navegue para a página de configuração da instância de SQL Managed Instance que pretende proteger. Sob **Segurança**, selecione **Security Center**.
3. Na página de configuração do Azure Defender para SQL
   - Ligue **o Azure** Defender para o SQL.
   - Configure os **alertas enviar para** o endereço de e-mail para receber alertas de segurança após a deteção de atividades de base de dados anómalas.
   - Selecione a **conta de armazenamento Azure** onde são guardados registos de auditoria de ameaças anómalas.
   - Selecione os **tipos de Proteção de Ameaças Avançadas** que gostaria de configurar. Saiba mais sobre [alertas avançados de proteção contra ameaças.](../database/threat-detection-overview.md)
4. Clique em **Guardar** para guardar a nova ou atualizada política do Azure Defender para a política SQL.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="criar proteção de ameaças avançadas":::

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Proteção Avançada de Ameaças.](../database/threat-detection-overview.md)
- Conheça as instâncias geridas, veja [O que é um Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Saiba mais sobre [a Advanced Threat Protection for Azure SQL Database](../database/threat-detection-configure.md).
- Saiba mais sobre [a auditoria da SQL Managed Instance](./auditing-configure.md).
- Saiba mais sobre [o Centro de Segurança Azure.](../../security-center/security-center-introduction.md)