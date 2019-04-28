---
title: Notas de versão do armazém de dados SQL do Azure | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: e77556ac0d6f64797906c0f3b4181f147b1668e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475073"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Notas de versão do Azure SQL Data Warehouse e atualizações da documentação

O Azure SQL Data Warehouse (SQL DW) é um com base na cloud Enterprise Data Warehouse que tira partido Massivamente paralela de processamento (MPP) para executar rapidamente consultas complexas em petabytes de dados. Utilize o SQL Data Warehouse como um componente fundamental de uma solução de macrodados. Importe macrodados para o SQL Data Warehouse com consultas simples de PolyBase T-SQL e, em seguida, utilize o poder do MPP para executar análises de elevado desempenho. À medida que integra e analisa, o armazém de dados tornar-se-á a única versão real com que a sua empresa pode contar para obter informações.

Clique nos links abaixo para obter mais informações sobre os novos recursos e aprimoramentos que pode esperar na versão mais recente do Azure SQL Data Warehouse. Pode esperar receber estas atualizações de serviço durante a sua agenda de manutenção identificados.

- [Março de 2019](./release-notes-10-0-10106-0.md#march-2019)
- [Janeiro de 2019](./release-notes-10-0-10106-0.md#january-2019)
- [Dezembro de 2018](./release-notes-10-0-10106-0.md#december-2018)
- [Outubro de 2018](./release-notes-10-0-10106-0.md#october-2018)
- [Setembro de 2018](./release-notes-september-2018.md)
- [Agosto de 2018](./release-notes-august-2018.md)
- [Julho de 2018](./release-notes-july-2018.md)
- [Junho de 2018](./release-notes-june-2018.md)
- [Maio de 2018](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>A verificar a versão de código que foi aplicada ao seu armazém de dados

Para confirmar que versão foi como foi aplicada ao seu armazém de dados. Ligar ao seu armazém de dados através do SSMS e execute a seguinte sintaxe para devolver a versão atual do SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Exemplo de saída: ![Versão do SQL Data Warehouse](./media/release-notes/sql_data_warehouse_version.png)

Utilize a data identificada para confirmar que versão foi aplicada ao seu armazém de dados SQL do Azure. 


## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) sobre a visualização de um agendamento de manutenção. 
- [Saiba mais](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) sobre como alterar uma agenda de manutenção.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) sobre como criar, visualizar e gerir alertas com o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre as ações de webhook para regras de alerta de registo.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre o Azure Service Health