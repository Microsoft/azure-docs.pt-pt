---
title: O serviço de importação/exportação leva muito tempo
description: O serviço de importação/exportação do banco de dados SQL do Azure leva muito tempo para importar ou exportar um banco de dados
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: e1638c9779ca50507a1ce30dd3bbc9c18248964a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807073"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>O serviço de importação/exportação do banco de dados SQL do Azure leva muito tempo para importar ou exportar um banco de dados

Quando você usa o serviço de importação/exportação do banco de dados SQL do Azure, o processo pode demorar mais do que o esperado. Este artigo descreve as possíveis causas para esse atraso e métodos de solução alternativa alternativos.

## <a name="azure-sql-database-importexport-service"></a>Serviço de importação/exportação do banco de dados SQL do Azure

O serviço de importação/exportação do banco de dados SQL do Azure é um serviço Web baseado em REST que é executado em cada data center do Azure. Esse serviço é chamado quando você usa a opção [importar banco de dados](sql-database-import.md#using-azure-portal) ou [Exportar](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) para mover o banco de dados SQL no portal do Azure. O serviço fornece enfileiramento de solicitações e serviços de computação gratuitos para executar importações e exportações entre um banco de dados SQL do Azure e o armazenamento de BLOBs do Azure

As operações de importação e exportação não representam um backup de banco de dados físico tradicional, mas sim um backup lógico do banco de dados que usa um formato BACPAC especial. O formato BACPAC permite que você evite ter que usar um formato físico que possa variar entre as versões do Microsoft SQL Server e do banco de dados SQL do Azure. Portanto, você pode usá-lo para restaurar com segurança o banco de dados para um banco de dados SQL Server e para um banco de dados SQL.

## <a name="what-causes-delays-in-the-process"></a>O que causa atrasos no processo?

O serviço de importação/exportação do banco de dados SQL do Azure fornece um número limitado de VMs (máquinas virtuais) de computação por região para processar operações de importação e exportação. As VMs de computação são hospedadas por região para garantir que a importação ou exportação Evite atrasos e encargos de largura de banda entre regiões. Se muitas solicitações forem feitas ao mesmo tempo na mesma região, poderão ocorrer atrasos significativos no processamento das operações. O tempo necessário para concluir as solicitações pode variar de alguns segundos a muitas horas.

> [!NOTE]
> Se uma solicitação não for processada dentro de quatro dias, o serviço cancelará automaticamente a solicitação.

## <a name="recommended-solutions"></a>Soluções recomendadas

Se suas exportações de banco de dados forem usadas somente para recuperação de exclusão acidental de dado, todas as edições do banco de dados SQL do Azure fornecerão o recurso de restauração de autoatendimento de backups gerados pelo sistema. Mas se você precisar dessas exportações por outros motivos, e se precisar de um desempenho de importação/exportação consistentemente mais rápido ou previsível, considere as seguintes opções:

* [Exporte para um arquivo BACPAC usando o utilitário SqlPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportar para um arquivo BACPAC usando SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Execute a importação ou exportação de BACPAC diretamente em seu código usando a API Microsoft SQL Server Data-Tier Application Framework (DacFx). Para obter informações adicionais, consulte:
  * [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Namespace Microsoft. SqlServer. Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Baixar o DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Itens a serem considerados ao exportar ou importar um banco de dados SQL do Azure

* Todos os métodos discutidos neste artigo usam a cota de DTU (unidade de transação de banco de dados), que causa limitação pelo serviço de banco de dados SQL do Azure. Você pode [exibir as estatísticas de DTU para o banco de dados no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Se o banco de dados tiver atingido seus limites de recursos, [atualize a camada de serviço](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) para adicionar mais recursos.
* O ideal é que você execute aplicativos cliente (como o utilitário SqlPackage ou seu aplicativo de DAC personalizado) de uma VM na mesma região que o banco de dados SQL. Caso contrário, você poderá ter problemas de desempenho relacionados à latência de rede.
* A exportação de tabelas grandes sem índices clusterizados pode ser muito lenta ou até mesmo causar falha. Esse comportamento ocorre porque a tabela não pode ser dividida e exportada em paralelo. Em vez disso, ele deve ser exportado em uma única transação e isso causa um baixo desempenho e falha potencial durante a exportação, especialmente para tabelas grandes.


## <a name="related-documents"></a>Documentos relacionados

[Considerações ao exportar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
