---
title: Configurar a deteção de ameaças - instância gerida de base de dados SQL do Azure | Documentos da Microsoft
description: Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados numa instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 59a3b4a4e1b08a9a9985836a9f9be44d1eff9c71
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472070"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurar a deteção de ameaças (pré-visualização) na instância gerida de base de dados SQL do Azure

SQL do Azure [deteção de ameaças](sql-database-threat-detection-overview.md) para [SQL Database Managed Instance](sql-database-managed-instance-index.yml) Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de bases de dados. Deteção de ameaças pode identificar **injeção SQL potencial**, **acesso a partir do Centro de dados ou localização invulgar**, **acesso a partir de familiarizado aplicativo de principal ou potencialmente prejudicial**, e **credenciais SQL de força bruta** -veja mais detalhes no [alertas de deteção de ameaças](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Deteção de ameaças](sql-database-threat-detection-overview.md) faz parte da [segurança avançada de dados do SQL](sql-advanced-threat-protection.md) (ADS) oferta, que é um pacote unificado para funções de segurança avançadas do SQL. Deteção de ameaças pode ser acessada e gerenciada através do portal de anúncios de SQL central. O serviço de deteção de ameaças é cobrado 15$ / mês por instância gerida, com os primeiros 30 dias sem encargos.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurar a deteção de ameaças da sua instância gerida no portal do Azure

1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com).
2. Navegue para a página de configuração de instância gerida que pretende proteger. Na **configurações** página, selecione **deteção de ameaças**.
3. Na página de configuração de deteção de ameaças
   - Ative **ON** deteção de ameaças.
   - Configurar o **lista de e-mails** para receber alertas de segurança após a deteção de atividades anómalas da base de dados.
   - Selecione o **conta de armazenamento do Azure** onde são guardados os registos de auditoria de ameaças anómalas.
4. Clique em **guardar** para guardar a política de deteção de ameaças novos ou atualizados.

   ![Deteção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [deteção de ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre a instância gerida, veja [o que é uma instância gerida](sql-database-managed-instance.md).
- Saiba mais sobre [deteção para a base de dados de ameaças](sql-database-threat-detection.md).
- Saiba mais sobre [instância gerida auditoria](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
