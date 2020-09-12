---
title: Apoio à movimentação de recursos Azure SQL entre regiões com Azure Resource Mover.
description: Reveja o apoio à movimentação de recursos Azure SQL entre regiões com a Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652998"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Apoio à deslocação de recursos Azure SQL entre regiões de Azure

Este artigo resume o apoio e os pré-requisitos para a movimentação de recursos Azure SQL entre as regiões de Azure com o Azure Resource Mover.

## <a name="requirements"></a>Requisitos

Os requisitos são resumidos no quadro seguinte.

**Funcionalidade** | **Suportado/Não apoiado** | **Detalhes**
--- | --- | ---
**Hiperescala da base de dados Azure SQL** | Não suportado | Não é possível mover bases de dados no nível de serviço de hiperescala Azure SQL com o Resource Mover.
**Redundância entre zonas** | Suportado |  Opções de movimento suportadas:<br/><br/> - Entre regiões que apoiam o despedimento da zona.<br/><br/> - Entre regiões que não apoiam a redundância da zona.<br/><br/> - Entre uma região que apoia o despedimento de zona para uma região que não apoia o despedimento de zonas.<br/><br/> - Entre uma região que não apoia o despedimento de zonas para uma região que apoia o despedimento de zonas. 
**Sincronização de dados** | Base de dados hub/sync: Não suportado<br/><br/> Membro sincronizado: Suportado. | Se um membro sincronizado for movido, é necessário configurar a sincronização de dados para a nova base de dados-alvo.
**Geo-replicação existente** | Suportado | As réplicas geo-geo existentes são rempeitadas para as novas primárias na região alvo.<br/><br/> A sementeira deve ser inicializada após a mudança. [Saiba mais](/azure/sql-database/sql-database-active-geo-replication-portal)
**Encriptação de dados transparente (TDE) com Bring Your Own Key (BYOK)** | Suportado | [Saiba mais](../key-vault/general/move-region.md) sobre a movimentação de cofres-chave através das regiões.
**TDE com chave gerida pelo serviço** | Suportado. |  [Saiba mais](../key-vault/general/move-region.md) sobre a movimentação de cofres-chave através das regiões.
**Regras dinâmicas de mascaramento de dados** | Suportado. | As regras são automaticamente copiadas para a região-alvo como parte da mudança. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal).
**Segurança de dados avançada** | Não suportado. | Solução: Configurar ao nível do SQL Server na região alvo. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).
**Regras da firewall** | Não suportado. | Solução: Estabeleça regras de firewall para o SQL Server na região alvo. As regras de firewall ao nível da base de dados são copiadas do servidor de origem para o servidor alvo. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule).
**Políticas de auditoria** | Não suportado. | As políticas serão reiniciadas para o padrão após a mudança. [Aprenda](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a reiniciar.
**Retenção da cópia de segurança** | Suportado. | As políticas de retenção de backup para a base de dados de origem são transitadas para a base de dados-alvo. [Aprenda](/azure/sql-database/sql-database-long-term-backup-retention-configure) a modificar as definições após o movimento.
**Afinação automática** | Não suportado. | Solução: Defina as definições de afinação automática após o movimento. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable).
**Alertas de base de dados** | Não suportado. | Solução: Desloque os alertas após a mudança. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal).
**Base de dados de alongamento do Azure SQL Server** | Não suportado | Não é possível mover bases de dados de alongamento de servidor SQL com o Resource Mover.
**Azure Synapse Analytics** | Não suportado | Não é possível mover a Synapse Analytics (anteriormente Azure SQL Data Warehouse) com o Resource Mover.
## <a name="next-steps"></a>Próximos passos

Experimente [os recursos Azure SQL](tutorial-move-region-sql.md) para outra região com o Resource Mover.