---
title: Exemplo de aplicativo multiinquilino - Wingtip SaaS
description: Aprenda usando uma aplicação multiarrendatária de amostra que utiliza a Base de Dados Azure SQL, o exemplo Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256500"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introdução a uma aplicação SaaS multiarrendatária que utiliza o padrão de base de dados por inquilino com Base de Dados SQL

A aplicação Wingtip SaaS é uma aplicação multiarrendatária de amostra. A aplicação utiliza o padrão de aplicação SaaS de base de dados por inquilino para atender vários inquilinos. A aplicação apresenta funcionalidades da Base de Dados Azure SQL que permitem cenários SaaS utilizando vários padrões de design e gestão do SaaS. Para se levantar rapidamente, a aplicação Wingtip SaaS implanta-se em menos de cinco minutos.

O código fonte de aplicação e os scripts de gestão estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Antes de começar, consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md) para passos para descarregar e desbloquear os scripts de gestão de Bilhetes Wingtip.

## <a name="application-architecture"></a>Arquitetura da aplicação

A app Wingtip SaaS utiliza o modelo base de dados por inquilino. Utiliza piscinas elásticas SQL para maximizar a eficiência. Para fornecer e mapear inquilinos aos seus dados, é utilizada uma base de dados de catálogo. A aplicação core Wingtip SaaS usa uma piscina com três inquilinos de amostra, mais a base de dados do catálogo. Os servidores do catálogo e dos inquilinos foram aprovisionados com pseudónimos dNS. Estes pseudónimos são utilizados para manter uma referência aos recursos ativos utilizados pela aplicação Wingtip. Estes pseudónimos são atualizados para apontar recursos de recuperação nos tutoriais de recuperação de desastres. Completar muitos dos tutoriais Wingtip SaaS resulta em complementos à implementação inicial. São introduzidos addons como bases de dados analíticas e gestão de esquemas de base de dados cruzadas.


![Arquitetura Wingtip SaaS](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


À medida que passa pelos tutoriais e trabalha com a app, concentre-se nos padrões SaaS à medida que se relacionam com o nível de dados. Por outras palavras, concentre-se no nível de dados e não analise demasiado a aplicação em si. Compreender a implementação destes padrões SaaS é fundamental para implementar estes padrões nas suas aplicações. Considere também quaisquer modificações necessárias para os seus requisitos de negócio específicos.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais SQL Database Wingtip SaaS

Depois de implementar a aplicação, explore os seguintes tutoriais que se baseiam na implementação inicial. Estes tutoriais exploram padrões Comuns saaS que aproveitam as características incorporadas da Base de Dados SQL, Do Armazém de Dados Azure SQL e outros serviços Azure. Os tutoriais incluem scripts PowerShell com explicações detalhadas. As explicações simplificam a compreensão e implementação dos mesmos padrões de gestão SaaS nas suas aplicações.


| Tutorial | Descrição |
|:--|:--|
| [Orientação e dicas para o exemplo da aplicação SQL Database multitenant SaaS](saas-tenancy-wingtip-app-guidance-tips.md) | Descarregue e execute os scripts PowerShell para preparar partes da aplicação. |
|[Implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)|  Desloque e explore a aplicação Wingtip SaaS com a sua subscrição Azure. |
|[Inquilinos de provisão e catálogo](saas-dbpertenant-provision-and-catalog.md)| Saiba como a aplicação se conecta aos inquilinos utilizando uma base de dados de catálogo e como o catálogo mapeia os inquilinos aos seus dados. |
|[Monitorizar e gerir o desempenho](saas-dbpertenant-performance-monitoring.md)| Aprenda a utilizar as funcionalidades de monitorização da Base de Dados SQL e defina alertas quando os limiares de desempenho são ultrapassados. |
|[Monitorizar com os registos do Azure Monitor](saas-dbpertenant-log-analytics.md) | Aprenda a usar [os registos do Monitor Azure](../log-analytics/log-analytics-overview.md) para monitorizar grandes quantidades de recursos em várias piscinas. |
|[Restaurar um único inquilino](saas-dbpertenant-restore-single-tenant.md)| Aprenda a restaurar uma base de dados de inquilinos para um ponto de acesso anterior. Também saiba como restaurar uma base de dados paralela, o que deixa a base de dados de inquilinos existente online. |
|[Gerir o esquema da base de dados dos inquilinos](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar dados de referência em todas as bases de dados dos inquilinos. |
|[Executar consultas distribuídas por inquilinos cruzados](saas-tenancy-cross-tenant-reporting.md) | Crie uma base de dados de análise ad hoc e execute consultas distribuídas em tempo real por todos os inquilinos.  |
|[Executar análises em dados extraídos do inquilino](saas-tenancy-tenant-analytics.md) | Extrair dados de inquilinos numa base de dados de análise ou num armazém de dados para consultas de análise offline. |


## <a name="next-steps"></a>Passos seguintes

- [Orientação geral e dicas quando você implanta e usa o exemplo da app Wingtip Tickets SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Implementar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
