---
title: O serviço de importação/exportação do banco de dados SQL do Azure leva muito tempo para importar ou exportar um banco de dados | Microsoft Docs
description: O serviço de importação/exportação do banco de dados SQL do Azure leva muito tempo para importar ou exportar um banco de dados
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974464"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>O serviço de importação/exportação do banco de dados SQL do Azure leva muito tempo para importar ou exportar um banco de dados

Ao usar o serviço de importação/exportação do banco de dados SQL do Azure, você pode observar que, às vezes, o processo pode levar muito tempo para ser concluído. Este artigo fornece informações adicionais sobre as possíveis causas de atrasos de três e métodos alternativos que você pode usar para solucionar esses problemas.

## <a name="azure-sql-database-importexport-service"></a>Serviço de importação/exportação do banco de dados SQL do Azure

O serviço de importação/exportação do banco de dados SQL do Azure é um serviço Web baseado em REST que é executado em cada Microsoft Azure data center. Esse é o serviço que é chamado quando você usa as opções [importar banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) ou [Exportar](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) para mover o banco de dados SQL no portal do Microsoft Azure. O serviço fornece um serviço de enfileiramento de solicitações gratuito e um serviço de computação gratuito para executar importações e exportações de um banco de dados SQL Microsoft Azure para Microsoft Azure armazenamento BLOB (objeto binário grande).

As operações de importação e exportação não são um backup de banco de dados físico tradicional, mas um backup lógico do banco de dados que usa um formato BACPAC especial. Esse formato de BACPAC lógico permite que você evite ter que usar um formato físico que possa variar entre as versões do SQL Server e do banco de dados SQL. Portanto, você pode usá-lo para restaurar com segurança o banco de dados para um banco de dados SQL e para um banco de dados SQL Server.

## <a name="what-causes-the-process-to-take-a-long-time"></a>O que faz com que o processo demore muito tempo

O serviço de importação/exportação do banco de dados SQL do Azure fornece um número limitado de VMs (máquinas virtuais) de computação por região para processar as operações de importação e exportação. A VM de computação é hospedada por região para garantir que a importação ou exportação Evite atrasos e encargos de largura de banda entre regiões. Portanto, se muitas solicitações forem feitas ao mesmo tempo na mesma região, ocorrerão atrasos significativos no processamento das operações. O tempo necessário para concluir as solicitações pode variar de alguns segundos a muitas horas.

> [!NOTE]
> Se uma solicitação não for processada dentro de quatro dias, o serviço cancelará automaticamente a solicitação.

## <a name="recommended-solutions"></a>Soluções recomendadas

Se suas exportações de banco de dados forem usadas somente para recuperação de exclusão acidental de dado, todas as edições de banco de dados do Azure SQL Server fornecem recursos de restauração de autoatendimento de backups gerados pelo sistema. Mas se você precisar dessas exportações por outros motivos, e se precisar de um desempenho de importação ou exportação consistentemente mais rápido ou previsível, considere as seguintes opções:

* [Exportar para um arquivo BACPAC usando o utilitário SqlPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exportar para um arquivo BACPAC usando SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Execute a importação ou exportação de BACPAC diretamente em seu código usando o Microsoft® SQL Server® API de DacFx (estrutura de aplicativo da camada de dados). Para análise de informações adicionais
  * [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Namespace Microsoft. SqlServer. Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Baixar o DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Considerações ao exportar ou importar um banco de dados SQL do Azure

* Todos os métodos discutidos neste artigo usam a cota de DTU, resultando na limitação pelo serviço SQLDB do Azure. Você pode [exibir as estatísticas de DTU para o banco de dados no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Se o banco de dados estiver atingindo os limites de recursos, [atualize a camada de serviço](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) para adicionar mais recursos.
* Os aplicativos cliente (como o utilitário SqlPackage ou seu aplicativo DAC personalizado) devem, idealmente, ser executados de uma VM (máquina virtual) na mesma região que o banco de dados SQL ou, caso contrário, você poderá encontrar problemas de desempenho devido à latência de rede.
* A exportação de tabelas grandes sem índices clusterizados pode ser muito lenta ou até mesmo resultar em falha. Isso ocorre porque a tabela não pode ser dividida e exportada em paralelo e deve ser exportada em uma única transação e causa o lentidão e as falhas em potencial durante a exportação, especialmente para tabelas grandes. 


## <a name="related-documents"></a>Documentos relacionados

[Considerações ao exportar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
