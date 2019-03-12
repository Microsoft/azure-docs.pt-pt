---
title: Configurar a deteção de ameaças - base de dados do SQL do Azure geridos instância | Documentos da Microsoft
description: Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados numa instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0c6bf22527278d0d2217a8b384eb8bcd2210856a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779522"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurar a deteção de ameaças (pré-visualização) na instância gerida da base de dados do Azure SQL

[Deteção de ameaças](sql-database-threat-detection-overview.md) para uma [instância gerida](sql-database-managed-instance-index.yml) Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de bases de dados. Deteção de ameaças pode identificar **injeção SQL potencial**, **acesso a partir do Centro de dados ou localização invulgar**, **acesso a partir de familiarizado aplicativo de principal ou potencialmente prejudicial**, e **credenciais SQL de força bruta** -veja mais detalhes no [alertas de deteção de ameaças](sql-database-threat-detection-overview.md#threat-detection-alerts).

Pode receber notificações sobre as ameaças detetadas através de [notificações por e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Deteção de ameaças](sql-database-threat-detection-overview.md) faz parte dos [dados de segurança avançada](sql-database-advanced-data-security.md) (ADS) oferta, que é um pacote unificado para funções de segurança avançadas do SQL. Deteção de ameaças pode ser acessada e gerenciada através do portal de anúncios de SQL central. O serviço de deteção de ameaças é cobrado 15$ / mês por instância gerida, com os primeiros 30 dias sem encargos.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurar a deteção de ameaças para a sua instância gerida no portal do Azure

1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com).
2. Navegue para a página de configuração da instância gerida que pretende proteger. Na **configurações** página, selecione **deteção de ameaças**.
3. Na página de configuração de deteção de ameaças
   - Ative **ON** deteção de ameaças.
   - Configurar o **lista de e-mails** para receber alertas de segurança após a deteção de atividades anómalas da base de dados.
   - Selecione o **conta de armazenamento do Azure** onde são guardados os registos de auditoria de ameaças anómalas.
4. Clique em **guardar** para guardar a política de deteção de ameaças novos ou atualizados.

   ![Deteção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [deteção de ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre as instâncias geridas, consulte [o que é uma instância gerida](sql-database-managed-instance.md).
- Saiba mais sobre [deteção para a base de dados de ameaças](sql-database-threat-detection.md).
- Saiba mais sobre [geridos a auditoria de instância](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
