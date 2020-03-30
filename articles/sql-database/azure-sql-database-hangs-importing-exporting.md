---
title: Serviço de Importação/Exportação demora muito tempo
description: Serviço de importação/exportação de base de dados Azure SQL demora muito tempo a importar ou exportar uma base de dados
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535770"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Serviço de importação/exportação de base de dados Azure SQL demora muito tempo a importar ou exportar uma base de dados

Quando utiliza o serviço de importação/exportação de bases de dados Azure SQL, o processo pode demorar mais do que o esperado. Este artigo descreve as causas potenciais para este atraso e métodos alternativos de supor.

## <a name="azure-sql-database-importexport-service"></a>Serviço de importação/exportação de base de dados Azure SQL

O serviço de importação/exportação de bases de dados Azure SQL é um serviço web baseado em REST que funciona em todos os centros de dados Azure. Este serviço é chamado quando utiliza a base de [dados importada](sql-database-import.md#using-azure-portal) ou a opção [Export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) para mover a sua base de dados SQL no portal Azure. O serviço fornece serviços de fila e cálculo gratuitos para realizar importações e exportações entre uma base de dados Azure SQL e armazenamento azure Blob.

As operações de importação e exportação não representam uma base de dados física tradicional, mas sim uma cópia de segurança lógica da base de dados que utiliza um formato BACPAC especial. O formato BACPAC permite evitar ter de utilizar um formato físico que pode variar entre as versões do Microsoft SQL Server e da Base de Dados Azure SQL. Por isso, pode utilizá-la para restaurar com segurança a base de dados de uma base de dados do SQL Server e para uma base de dados SQL.

## <a name="what-causes-delays-in-the-process"></a>O que causa atrasos no processo?

O serviço de importação/exportação de bases de dados Azure SQL fornece um número limitado de máquinas virtuais de computação (VMs) por região para processar operações de importação e exportação. Os VM sem estão alojados por região para garantir que a importação ou a exportação evitem atrasos e encargos de largura de banda transversal. Se forem feitos demasiados pedidos ao mesmo tempo na mesma região, podem ocorrer atrasos significativos no processamento das operações. O tempo necessário para completar os pedidos pode variar de alguns segundos a muitas horas.

> [!NOTE]
> Se um pedido não for processado no prazo de quatro dias, o serviço cancela automaticamente o pedido.

## <a name="recommended-solutions"></a>Soluções recomendadas

Se as exportações da sua base de dados forem utilizadas apenas para a recuperação da eliminação acidental de dados, todas as edições da Base de Dados Azure SQL fornecem capacidade de restauração de self-service a partir de cópias de segurança geradas pelo sistema. Mas se precisar destas exportações por outras razões, e se necessitar consistentemente de um desempenho de importação/exportação consistentemente mais rápido ou previsível, considere as seguintes opções:

* [Exportar para um ficheiro BACPAC utilizando o utilitário SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportar para um ficheiro BACPAC utilizando o SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Execute a importação ou exportação BACPAC diretamente no seu código utilizando a API de aplicação de nível de dados do Microsoft SQL Server (DacFx). Para obter informações adicionais, consulte:
  * [Exportar uma aplicação a nível de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac Namespace](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Baixar DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Coisas a considerar quando exporta ou importa uma base de dados Azure SQL

* Todos os métodos discutidos neste artigo utilizam a quota da Unidade de Transações de Bases de Dados (DTU), que provoca estrangulamento pelo serviço de base de dados Azure SQL. Pode [ver as estatísticas do DTU para a base](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring)de dados no portal Azure . Se a base de dados tiver atingido os seus limites de recursos, [atualize o nível de serviço](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) para adicionar mais recursos.
* Idealmente, deve executar aplicações de clientes (como o utilitário sqlpackage ou a sua aplicação PERSONALIZADA DAC) a partir de um VM na mesma região que a sua base de dados SQL. Caso contrário, poderá experimentar problemas de desempenho relacionados com a latência da rede.
* Exportar grandes tabelas sem índices agrupados pode ser muito lento ou mesmo causar falhas. Este comportamento ocorre porque a mesa não pode ser dividida e exportada em paralelo. Em vez disso, deve ser exportada numa única transação, o que provoca um desempenho lento e uma potencial falha durante a exportação, especialmente para as grandes tabelas.


## <a name="related-documents"></a>Documentos relacionados

[Considerações ao exportar uma base de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
