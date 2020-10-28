---
title: Exemplo de aplicação multitenant - Wingtip SaaS
description: Aprenda utilizando uma aplicação multitenante de amostra que utiliza a Base de Dados Azure SQL, o exemplo wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 3abfffcfb47a9530b6a04762624eee9d6ee9bc81
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780466"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Introdução a uma app SaaS multitenante que utiliza o padrão de base de dados por inquilino com Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A aplicação Wingtip SaaS é uma aplicação multitenante de amostra. A aplicação utiliza o padrão de aplicação SaaS por insutá-lo para servir vários inquilinos. A aplicação apresenta funcionalidades da Base de Dados Azure SQL que permitem cenários saaS utilizando vários padrões de design e gestão saaS. Para se levantar rapidamente, a aplicação Wingtip SaaS implementa-se em menos de cinco minutos.

Os scripts de código fonte de aplicação e de gestão estão disponíveis no [repo WingtipTicketsSaAS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Antes de começar, consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts de gestão de Bilhetes Wingtip.

## <a name="application-architecture"></a>Arquitetura da aplicação

A aplicação Wingtip SaaS utiliza o modelo base de dados por inquilino. Utiliza piscinas elásticas SQL para maximizar a eficiência. Para o fornecimento e mapeamento dos inquilinos aos seus dados, é utilizada uma base de dados de catálogo. A aplicação Core Wingtip SaaS utiliza uma piscina com três inquilinos de amostra, além da base de dados do catálogo. O catálogo e os servidores de inquilinos foram a provisionados com pseudónimos DNS. Estes pseudónimos são utilizados para manter uma referência aos recursos ativos utilizados pela aplicação Wingtip. Estes pseudónimos são atualizados para apontar recursos de recuperação nos tutoriais de recuperação de desastres. Completar muitos dos tutoriais do Wingtip SaaS resulta em complementos à implementação inicial. São introduzidos complementos como bases de dados analíticas e gestão de esquemas de bases de dados cruzadas.


![Arquitetura SaaS wingtip](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


À medida que passa pelos tutoriais e trabalha com a app, foque-se nos padrões SaaS à medida que se relacionam com o nível de dados. Por outras palavras, concentre-se no nível de dados e não enalize demasiado a própria app. Compreender a implementação destes padrões SaaS é fundamental para implementar estes padrões nas suas aplicações. Considere também quaisquer modificações necessárias para os seus requisitos específicos de negócio.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais saas da asa da base de dados SQL

Depois de implementar a aplicação, explore os seguintes tutoriais que se baseiam na implementação inicial. Estes tutoriais exploram padrões saaS comuns que tiram partido das características incorporadas da BASE de Dados SQL, Azure Synapse Analytics (anteriormente SQL Data Warehouse) e outros serviços Azure. Os tutoriais incluem scripts PowerShell com explicações detalhadas. As explicações simplificam a compreensão e implementação dos mesmos padrões de gestão saaS nas suas aplicações.


| Tutorial | Descrição |
|:--|:--|
| [Orientação e dicas para o exemplo multitenante da app SaaS da SqL Database](saas-tenancy-wingtip-app-guidance-tips.md) | Descarregue e execute scripts PowerShell para preparar partes da aplicação. |
|[Implementar e explorar a aplicação Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md)|  Implemente e explore a aplicação Wingtip SaaS com a sua subscrição Azure. |
|[Arrendamentos de provisão e catálogo](./saas-dbpertenant-provision-and-catalog.md)| Saiba como a aplicação se conecta aos inquilinos utilizando uma base de dados de catálogo, e como o catálogo mapeia os inquilinos aos seus dados. |
|[Monitorizar e gerir o desempenho](./saas-dbpertenant-performance-monitoring.md)| Aprenda a utilizar as funcionalidades de monitorização da Base de Dados SQL e desemprete os alertas quando os limiares de desempenho forem ultrapassados. |
|[Monitorizar com os registos do Azure Monitor](./saas-dbpertenant-log-analytics.md) | Aprenda a usar [registos do Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) para monitorizar grandes quantidades de recursos em várias piscinas. |
|[Restaurar um único inquilino](./saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar uma base de dados de inquilinos a um ponto anterior no tempo. Também aprenda a restaurar para uma base de dados paralela, que deixa a base de dados de inquilinos existente on-line. |
|[Gerir o esquema da base de dados de inquilinos](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar os dados de referência em todas as bases de dados dos inquilinos. |
|[Executar consultas distribuídas pelo inquilino](saas-tenancy-cross-tenant-reporting.md) | Crie uma base de dados de análise ad hoc e execute consultas distribuídas em tempo real em todos os inquilinos.  |
|[Executar análises sobre dados de inquilinos extraídos](saas-tenancy-tenant-analytics.md) | Extrair dados do inquilino numa base de dados de análise ou armazém de dados para consultas de análise offline. |


## <a name="next-steps"></a>Passos seguintes

- [Orientação geral e dicas ao implementar e usar o exemplo da app Wingtip Tickets SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Implementar a aplicação Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md)