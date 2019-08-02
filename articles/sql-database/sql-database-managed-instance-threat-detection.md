---
title: Configurar detecção de ameaças-instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: A detecção de ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados em uma instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567305"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurar a detecção de ameaças (versão prévia) na instância gerenciada do banco de dados SQL do Azure

A [detecção de ameaças](sql-database-threat-detection-overview.md) para uma [instância gerenciada](sql-database-managed-instance-index.yml) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A detecção de ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso de aplicativo de entidade de segurança ou aplicativos potencialmente prejudiciais**desconhecidos e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de detecção de ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou [portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

A [detecção de ameaças](sql-database-threat-detection-overview.md) faz parte da oferta do ADS (segurança de [dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A detecção de ameaças pode ser acessada e gerenciada por meio do portal de anúncios do SQL central. O serviço de detecção de ameaças é cobrado em 15 $/mês por instância gerenciada, com os primeiros 30 dias gratuitos.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configure a detecção de ameaças para sua instância gerenciada no portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração da instância gerenciada que você deseja proteger. Na página **configurações** , selecione **detecção de ameaças**.
3. Na página configuração de detecção de ameaças
   - Ativar a detecção **de** ameaças.
   - Configure a **lista de emails** para receber alertas de segurança após a detecção de atividades anormais do banco de dados.
   - Selecione a **conta de armazenamento do Azure** em que os registros de auditoria de ameaças Anômalas são salvos.
4. Clique em **salvar** para salvar a política de detecção de ameaças nova ou atualizada.

   ![detecção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [detecção de ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre instâncias gerenciadas, consulte [o que é uma instância gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [a detecção de ameaças para um banco de dados individual](sql-database-threat-detection.md).
- Saiba mais sobre a [auditoria de instância gerenciada](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
