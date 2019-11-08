---
title: Exemplo de aplicativo multilocatário – Wingtip SaaS
description: Aprenda usando um aplicativo multilocatário de exemplo que usa o banco de dados SQL do Azure, o exemplo de SaaS Wingtip
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822171"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introdução a um aplicativo SaaS multilocatário que usa o padrão de banco de dados por locatário com o banco de dados SQL

O aplicativo SaaS Wingtip é um aplicativo multilocatário de exemplo. O aplicativo usa o padrão de aplicativo SaaS de banco de dados por locatário para atender a vários locatários. O aplicativo apresenta recursos do banco de dados SQL do Azure que habilitam cenários de SaaS usando vários padrões de design e gerenciamento de SaaS. Para colocar rapidamente em funcionamento, o aplicativo SaaS do Wingtip é implantado em menos de cinco minutos.

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Antes de começar, consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts de gerenciamento da Wingtip tickets.

## <a name="application-architecture"></a>Arquitetura da aplicação

O aplicativo SaaS Wingtip usa o modelo de banco de dados por locatário. Ele usa pools elásticos do SQL para maximizar a eficiência. Para provisionar e mapear locatários para seus dados, é usado um banco de dado de catálogo. O aplicativo SaaS da Wingtip principal usa um pool com três locatários de exemplo, além do banco de dados do catálogo. Os servidores de catálogo e locatário foram provisionados com aliases de DNS. Esses aliases são usados para manter uma referência aos recursos ativos usados pelo aplicativo Wingtip. Esses aliases são atualizados para apontar para recursos de recuperação nos tutoriais de recuperação de desastre. A conclusão de muitos dos tutoriais de SaaS do Wingtip resulta em Complementos na implantação inicial. Os complementos, como bancos de dados analíticos e o gerenciamento de esquema entre bancos, são introduzidos.


![Arquitetura SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com o aplicativo, concentre-se nos padrões de SaaS conforme eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não analise o aplicativo em si. Compreender a implementação desses padrões de SaaS é fundamental para implementar esses padrões em seus aplicativos. Considere também as modificações necessárias para seus requisitos específicos de negócios.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais de SaaS do banco de dados SQL Wingtip

Depois de implantar o aplicativo, explore os tutoriais a seguir que se baseiam na implantação inicial. Esses tutoriais exploram os padrões de SaaS comuns que aproveitam os recursos internos do banco de dados SQL, do Azure SQL Data Warehouse e de outros serviços do Azure. Os tutoriais incluem scripts do PowerShell com explicações detalhadas. As explicações simplificam a compreensão e a implementação dos mesmos padrões de gerenciamento de SaaS em seus aplicativos.


| Tutorial | Descrição |
|:--|:--|
| [Diretrizes e dicas para o exemplo de aplicativo SaaS multilocatário do banco de dados SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Baixe e execute scripts do PowerShell para preparar partes do aplicativo. |
|[Implantar e explorar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)|  Implante e explore o aplicativo SaaS Wingtip com sua assinatura do Azure. |
|[Provisionar e catalogar locatários](saas-dbpertenant-provision-and-catalog.md)| Saiba como o aplicativo se conecta a locatários usando um banco de dados de catálogo e como o catálogo mapeia locatários para seus dados. |
|[Monitorar e gerenciar o desempenho](saas-dbpertenant-performance-monitoring.md)| Saiba como usar os recursos de monitoramento do banco de dados SQL e definir alertas quando os limites de desempenho forem excedidos. |
|[Monitorar com logs de Azure Monitor](saas-dbpertenant-log-analytics.md) | Saiba como usar [os logs de Azure monitor](../log-analytics/log-analytics-overview.md) para monitorar grandes quantidades de recursos em vários pools. |
|[Restaurar um único inquilino](saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar um banco de dados de locatário para um ponto anterior no tempo. Saiba também como restaurar para um banco de dados paralelo, que deixa o banco de dados de locatário existente online. |
|[Gerenciar esquema de banco de dados de locatário](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar os dados de referência em todos os bancos de dado de locatário. |
|[Executar consultas distribuídas entre locatários](saas-tenancy-cross-tenant-reporting.md) | Crie um banco de dados de análise ad hoc e execute consultas distribuídas em tempo real em todos os locatários.  |
|[Executar análise em dados de locatário extraídos](saas-tenancy-tenant-analytics.md) | Extraia dados de locatário em um banco de dados de análise ou data warehouse para consultas de análise offline. |


## <a name="next-steps"></a>Passos seguintes

- [Diretrizes gerais e dicas ao implantar e usar o exemplo de aplicativo Wingtip tickets SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Implantar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
