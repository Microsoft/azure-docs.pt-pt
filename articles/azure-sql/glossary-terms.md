---
title: Glossário de termos
titleSuffix: Azure SQL
description: Um glossário de termos para trabalhar com Azure SQL Database, Azure SQL Managed Instance, e SQL em Azure VM.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: f05871c41e2deec3f6a52446844c0b8fd2c9038d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221392"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Glossário de termos da Base de Dados Azure SQL
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

|Contexto|Termo|Mais informações|
|:---|:---|:---|
|Serviço do Azure|Base de Dados Azure SQL ou Base de Dados SQL|[Base de Dados SQL do Azure](database/sql-database-paas-overview.md)|
|Modelo de compra|Modelo de compra baseado em DTU|[Modelo de compra baseado em DTU](database/service-tiers-dtu.md)|
||Modelo de compra baseado em vCore|[Modelo de compra baseado em vCore](database/service-tiers-vcore.md)|
|Opção de implementação |Base de dados individual|[Bases de dados individuais](database/single-database-overview.md)|
||Conjunto elástico|[Conjunto elástico](database/elastic-pool-overview.md)|
|Camada de serviços|Básico, Padrão, Premium, Finalidade Geral, Hiperescala, Negócio Crítico|Para os níveis de serviço no modelo vCore, consulte os [níveis de serviço SQL Database](database/service-tiers-vcore.md#service-tiers). Para os níveis de serviço no modelo DTU, consulte o [modelo DTU](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Nível de computação|Computação sem servidor|[Computação sem servidor](database/service-tiers-vcore.md#compute-tiers)
||Cálculo provisionado|[Cálculo provisionado](database/service-tiers-vcore.md#compute-tiers)
|Geração computacional|Gen5, Série M, Fsv2|[Gerações de hardware](database/service-tiers-vcore.md#hardware-generations)
|Entidade do servidor| Server |[Servidores SQL lógicos](database/logical-servers.md)|
|Tipo de recurso|vCore|Um núcleo CPU fornecido ao recurso compute para uma única base de dados, piscina elástica. |
||Tamanho do cálculo e quantidade de armazenamento|O tamanho do cálculo é a quantidade máxima de CPU, memória e outros recursos não relacionados com armazenamento disponíveis para uma única base de dados, ou piscina elástica.  O tamanho do armazenamento é a quantidade máxima de armazenamento disponível para uma única base de dados, ou piscina elástica. Para opções de dimensionamento no modelo vcore, consulte [bases de dados individuais vCore](database/resource-limits-vcore-single-databases.md)e [piscinas elásticas vCore](database/resource-limits-vcore-elastic-pools.md).  (.. /gestão de instância/limites de recursos.md).  Para obter opções de dimensionamento no modelo DTU, consulte [bases de dados únicas DTU](database/resource-limits-dtu-single-databases.md) e [piscinas elásticas DTU.](database/resource-limits-dtu-elastic-pools.md)

## <a name="azure-sql-managed-instance"></a>Instância Gerida do Azure SQL

|Contexto|Termo|Mais informações|
|:---|:---|:---|
|Serviço do Azure|Instância Gerida do Azure SQL|[Instância Gerida do SQL](managed-instance/sql-managed-instance-paas-overview.md)|
|Modelo de compra|Modelo de compra baseado em vCore|[Modelo de compra baseado em vCore](database/service-tiers-vcore.md)|
|Opção de implementação |Instância Única|[Instância única](managed-instance/sql-managed-instance-paas-overview.md)|
||Piscina de instância (pré-visualização)|[Conjuntos de instâncias](managed-instance/instance-pools-overview.md)|
|Camada de serviços|Propósito Geral, Critical De Negócios|[Níveis de serviço de instância gerida SQL](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Nível de computação|Cálculo provisionado|[Cálculo provisionado](database/service-tiers-vcore.md#compute-tiers)|
|Geração computacional|Gen5|[Gerações de hardware](database/service-tiers-vcore.md#hardware-generations)
|Entidade do servidor|Caso ou instância gerida| N/A como a SQL Managed Instance é por si só o servidor |
|Tipo de recurso|vCore|Um núcleo cpu fornecido ao recurso compute para SQL Managed Instance.|
||Tamanho do cálculo e quantidade de armazenamento|O tamanho do cálculo é a quantidade máxima de CPU, memória e outros recursos não relacionados com armazenamento para SQL Managed Instance.  O tamanho do armazenamento é a quantidade máxima de armazenamento disponível para uma SQL Managed Instance.  Para opções de dimensionamento, [SQL Managed Instances](managed-instance/resource-limits.md). |

## <a name="sql-on-azure-vm"></a>SQL em Azure VM

precisa de mais coisas aqui
