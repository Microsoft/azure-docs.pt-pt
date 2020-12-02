---
title: Azure Defender para SQL - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para a SQL.
author: memildin
ms.author: memildin
ms.date: 11/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 102d4916caf9dcae577006b6b847979f98ab3673
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499905"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introdução ao Azure Defender para o SQL

O Azure Defender for SQL inclui dois planos do Azure Defender que alargam o pacote de segurança de [dados](../azure-sql/database/azure-defender-for-sql.md) do Azure Security Center para proteger as suas bases de dados e os seus dados onde quer que estejam localizados. 

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|**Azure Defender para servidores de base de dados Azure SQL** - Geralmente disponível (GA)<br>**Azure Defender para servidores SQL em máquinas** - Pré-visualização<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preços:|Os dois planos que formam **o Azure Defender para o SQL** são faturados como mostrado na [página de preços](security-center-pricing.md)|
|Versões SQL protegidas:|SQL em máquinas virtuais Azure - [Windows](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) e [Linux](../azure-sql/virtual-machines/linux/sql-server-on-linux-vm-what-is-iaas-overview.md)<br>[Servidores SQL ativados pelo Arco](/sql/sql-server/azure-arc/overview) (inclui servidores SQL no local)<br>Bases [de dados únicas](../azure-sql/database/single-database-overview.md) do Azure SQL e [piscinas elásticas](../azure-sql/database/elastic-pool-overview.md)<br>[Instância Gerida do SQL no Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics dedicada piscina SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) US Gov<br>![Não](./media/icons/no-icon.png) China Gov, Outro Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>O que a Azure Defender protege para a SQL?

**O Azure Defender para a SQL** compreende dois planos separados do Azure Defender:

- **O Azure Defender para servidores de base de dados Azure SQL** protege:
  - [Base de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)
  - [Instância Gerida do SQL no Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Piscina SQL dedicada em Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **O Azure Defender para servidores SQL em máquinas (Preview)** alarga as proteções para os seus Servidores SQL nativos do Azure para suportar totalmente ambientes híbridos e proteger servidores SQL (todas as versões suportadas) hospedados em Azure, outros ambientes em nuvem e até mesmo máquinas no local


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quais são os benefícios do Azure Defender para a SQL?

Estes dois planos incluem funcionalidade para identificar e mitigar potenciais vulnerabilidades de bases de dados e detetar atividades anómalas que possam indicar ameaças às suas bases de dados:

- [Avaliação de vulnerabilidade](../azure-sql/database/sql-vulnerability-assessment.md) - O serviço de digitalização para descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. As análises fornecem uma visão geral do estado de segurança das suas máquinas SQL e detalhes de quaisquer conclusões de segurança.

- [Proteção avançada de ameaças](../azure-sql/database/threat-detection-overview.md) - O serviço de deteção que monitoriza continuamente os seus servidores SQL para ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Este serviço fornece alertas de segurança orientados para a ação no Centro de Segurança Azure com detalhes da atividade suspeita, orientações sobre como mitigar as ameaças e opções para continuar as suas investigações com a Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Que tipo de alertas o Azure Defender para o SQL fornece?

Alertas de segurança são acionados quando há:

- **Potenciais ataques de injeção de SQL** - incluindo vulnerabilidades detetadas quando as aplicações geram uma declaração SQL defeituosa na base de dados
- **Padrões anómalos** de acesso e consulta de bases de dados - por exemplo, um número anormalmente elevado de tentativas de inscrição falhadas com credenciais diferentes (uma tentativa de força bruta)
- **Atividade de base de dados suspeita** - por exemplo, uma alteração no destino de armazenamento de exportação para uma operação de importação e exportação de SQL

Os alertas incluem detalhes do incidente que os desencadeou, bem como recomendações sobre como investigar e remediar ameaças.



## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre Azure Defender para a SQL.

Para obter material relacionado, consulte os seguintes artigos: 

- [Como ativar o Azure Defender para servidores SQL em máquinas](defender-for-sql-usage.md)
- [Como ativar o Azure Defender para servidores de base de dados SQL](../azure-sql/database/azure-defender-for-sql.md)
- [A lista de alertas do Azure Defender para o SQL](alerts-reference.md#alerts-sql-db-and-warehouse)
