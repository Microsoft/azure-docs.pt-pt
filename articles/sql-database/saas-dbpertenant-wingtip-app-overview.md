---
title: Exemplo de aplicação multi-inquilino de base de dados SQL do Azure - Wingtip SaaS | Documentos da Microsoft
description: Saiba mais através de um exemplo de aplicação multi-inquilino que utiliza o SQL Database do Azure, o exemplo de Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: c26f042cb749f9ab023e7033ea015b74d87ca92a
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214117"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introdução às aplicações SaaS multi-inquilino que utiliza o padrão de base de dados por inquilino com a base de dados SQL

A aplicação Wingtip SaaS é uma aplicação multi-inquilino de exemplo. A aplicação utiliza o padrão de aplicação SaaS da base de dados por inquilino para servir vários inquilinos. A aplicação apresenta funcionalidades da base de dados do Azure SQL que permitem cenários SaaS ao utilizar vários padrões de conceção e gestão de SaaS. Para obter rapidamente em execução, a aplicação Wingtip SaaS implementa em menos de cinco minutos.

Scripts de gestão e código de origem do aplicativo estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Antes de começar, consulte a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de gestão da Wingtip Tickets de desbloqueio.

## <a name="application-architecture"></a>Arquitetura da aplicação

A aplicação Wingtip SaaS utiliza o modelo de base de dados por inquilino. Ele usa conjuntos elásticos SQL para maximizar a eficiência. Para o aprovisionamento e inquilinos de mapeamento aos respetivos dados, é utilizada uma base de dados do catálogo. O núcleo de aplicação Wingtip SaaS utiliza um conjunto com três inquilinos de exemplo, bem como a base de dados do catálogo. Os servidores de catálogo e de inquilino tiveram sido aprovisionados com aliases de DNS. Estes aliases são utilizados para manter uma referência aos recursos do Active Directory utilizada pela aplicação Wingtip. Estes aliases são atualizadas para que apontem para os recursos de recuperação nos tutoriais de recuperação após desastre. A concluir muitos dos resultados de tutoriais do Wingtip SaaS suplementos para a implementação inicial. Suplementos, como bases de dados de análise e gestão de esquemas de entre bases de dados são introduzidos.


![Arquitetura do Wingtip SaaS](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


À medida que percorrer os tutoriais e trabalhar com a aplicação, enfocar os padrões de SaaS, como eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não overanalyze aplicação em si. Compreender a implementação desses SaaS padrões é a chave para implementar estes padrões nas suas aplicações. Considere também as eventuais modificações necessárias para os seus requisitos comerciais específicos.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SQL da base de dados Wingtip SaaS

Depois de implementar a aplicação, explore os tutoriais seguintes, que têm por base a implementação inicial. Estes tutoriais explore padrões SaaS comuns que tiram partido das funcionalidades incorporadas da base de dados SQL, Azure SQL Data Warehouse e outros serviços do Azure. Tutoriais incluem scripts do PowerShell com explicações detalhadas. As explicações simplificam a compreensão e implementação dos mesmos padrões de gestão de SaaS em seus aplicativos.


| Tutorial | Descrição |
|:--|:--|
| [Diretrizes e dicas para o exemplo de aplicação de SaaS multi-inquilino da base de dados SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Transfira e execute scripts do PowerShell para preparar as partes do aplicativo. |
|[Implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)|  Implementar e explorar a aplicação Wingtip SaaS com a sua subscrição do Azure. |
|[Aprovisionar e catalogar inquilinos](saas-dbpertenant-provision-and-catalog.md)| Saiba como o aplicativo se conecta aos inquilinos ao utilizar uma base de dados do catálogo e, como o catálogo mapeia os inquilinos aos respetivos dados. |
|[Monitorizar e gerir o desempenho](saas-dbpertenant-performance-monitoring.md)| Saiba como utilizar funcionalidades de monitorização de base de dados SQL e definir alertas quando os limiares de desempenho são excedidos. |
|[Monitorizar com os registos do Azure Monitor](saas-dbpertenant-log-analytics.md) | Aprenda a usar [registos do Azure Monitor](../log-analytics/log-analytics-overview.md) para monitorizar grandes quantidades de recursos em vários conjuntos. |
|[Restaurar um único inquilino](saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar uma base de dados do inquilino para um ponto anterior no tempo. Além disso, saiba como restaurar um banco de dados paralela, o que deixa a base de dados do inquilino existente online. |
|[Gerir esquema de banco de dados do inquilino](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar dados de referência em todas as bases de dados do inquilino. |
|[Executar consultas distribuídas de entre inquilinos](saas-tenancy-cross-tenant-reporting.md) | Criar uma base de dados de análise ad hoc e executar consultas distribuídas em tempo real em todos os inquilinos.  |
|[Executar análises em dados de inquilino extraído](saas-tenancy-tenant-analytics.md) | Extrair dados de inquilino para um armazém de dados ou base de dados de análise para consultas de análise offline. |


## <a name="next-steps"></a>Passos Seguintes

- [Orientações gerais e dicas quando implementar e utilizar o exemplo de aplicação Wingtip Tickets SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Implementar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
