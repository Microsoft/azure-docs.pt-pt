---
title: A importação e exportação de uma base de dados demora muito tempo
description: Azure SQL Database e Azure SQL Managed Instance Import/Export service demoram muito tempo a importar ou exportar uma base de dados
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 15b27990842da8837e6cd8fc8d29f361961ee044
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669550"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database and Managed Instance Import/Export service leva muito tempo para importar ou exportar uma base de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Quando utilizar o serviço de Importação/Exportação, o processo pode demorar mais do que o esperado. Este artigo descreve as causas potenciais para este atraso e métodos alternativos de solução.

## <a name="azure-sql-database-importexport-service"></a>Serviço de importação/exportação de base de dados Azure SQL

O serviço Azure SQL Database Import/Export é um serviço web baseado em REST que funciona em todos os centros de dados Azure. Este serviço é chamado quando utiliza a [base de dados Import](database-import.md#using-azure-portal) ou a opção [Export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) para mover a sua base de dados SQL no portal Azure. O serviço fornece serviços de fila gratuita e de computação para realizar importações e exportações entre a Base de Dados Azure SQL e o armazenamento Azure Blob.

As operações de importação e exportação não representam uma cópia de segurança física tradicional, mas sim uma cópia de segurança lógica da base de dados que utiliza um formato BACPAC especial. O formato BACPAC permite evitar ter de utilizar um formato físico que pode variar entre as versões do Microsoft SQL Server e da Azure SQL Database. Portanto, pode usá-lo para restaurar com segurança a base de dados para uma base de dados SQL Server e para uma base de dados SQL.

## <a name="what-causes-delays-in-the-process"></a>O que causa atrasos no processo?

O serviço Azure SQL Database Import/Export fornece um número limitado de máquinas virtuais computacional (VMs) por região para processar operações de importação e exportação. Os VM do cálculo são alojados por região para garantir que a importação ou exportação evite atrasos e encargos de largura de banda entre regiões. Se forem feitos demasiados pedidos ao mesmo tempo na mesma região, poderão ocorrer atrasos significativos no processamento das operações. O tempo necessário para completar os pedidos pode variar de alguns segundos a muitas horas.

> [!NOTE]
> Se um pedido não for processado no prazo de quatro dias, o serviço cancela automaticamente o pedido.

## <a name="recommended-solutions"></a>Soluções recomendadas

Se as exportações da sua base de dados forem utilizadas apenas para a recuperação de dados acidentais, todas as edições Azure SQL Database fornecem capacidade de restauração de autosserviço a partir de backups gerados pelo sistema. Mas se precisar destas exportações por outras razões, e se necessitar de um desempenho de importação/exportação consistentemente mais rápido ou mais previsível, considere as seguintes opções:

* [Exporte para um ficheiro BACPAC utilizando o utilitário SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportar para um ficheiro BACPAC utilizando o SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Executar a importação ou exportação BACPAC diretamente no seu código utilizando a API de Aplicação de Dados tier tier do Servidor Microsoft SQL (DacFx). Para obter informações adicionais, consulte:
  * [Exportar uma aplicação de nível de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac Namespace](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Baixar o DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Coisas a ter em conta quando exporta ou importa uma base de dados Azure SQL

* Todos os métodos discutidos neste artigo utilizam a quota da Unidade de Transações de Base de Dados (DTU), o que provoca estrangulamento pelo serviço de base de dados Azure SQL. Pode [ver as estatísticas do DTU para a base de dados no portal Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Se a base de dados tiver atingido os seus limites de recursos, [atualize o nível de serviço](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) para adicionar mais recursos.
* Idealmente, deve executar aplicações de clientes (como o utilitário sqlpackage ou a sua aplicação DAC personalizada) a partir de um VM na mesma região que a sua base de dados SQL. Caso contrário, poderá experimentar problemas de desempenho relacionados com a latência da rede.
* Exportar grandes tabelas sem índices agrupados pode ser muito lento ou mesmo causar falhas. Este comportamento ocorre porque a mesa não pode ser dividida e exportada paralelamente. Em vez disso, deve ser exportada numa única transação, o que provoca um desempenho lento e uma potencial falha durante a exportação, especialmente para os grandes quadros.


## <a name="related-documents"></a>Documentos relacionados

[Considerações na exportação de uma base de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
