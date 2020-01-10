---
title: Mover dados para um Azure SQL Database - Team Data Science Process
description: Mova dados de ficheiros simples (formatos de CSV ou TSV) ou de dados armazenados num servidor de SQL no local para uma base de dados SQL do Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/04/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 79ef5a6e4af3e0becc5dbeed9ea377e8ef0eb3d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432164"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para uma Base de Dados SQL do Azure para o Azure Machine Learning

Este artigo descreve as opções para mover dados de ficheiros simples (formatos CSV ou TSV) ou de dados armazenados num servidor de SQL no local para uma base de dados SQL do Azure. Estas tarefas para mover dados para a cloud fazem parte do processo de ciência de dados de equipa.

Para um tópico que descreve as opções para mover dados para um servidor de SQL no local para o Machine Learning, consulte [mover dados para o SQL Server numa máquina virtual do Azure](move-sql-server-virtual-machine.md).

A tabela seguinte resume as opções para mover dados para uma base de dados do SQL do Azure.

| <b>ORIGEM</b> | <b>DESTINO: Base de dados SQL do Azure</b> |
| --- | --- |
| <b>Ficheiro simples (CSV ou TSV formatado)</b> |[Consulta SQL de inserção em massa](#bulk-insert-sql-query) |
| <b>SQL Server no local</b> |1.[exportar para ficheiro simples](#export-flat-file)<br> 2. [Assistente de migração de banco de dados SQL](#insert-tables-bcp)<br> 3. [backup e restauração de banco de dados](#db-migration)<br> 4. [Azure data Factory](#adf) |

## <a name="prereqs"></a>Pré-requisitos
Os procedimentos descritos aqui exigem que tenha:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Utilize uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Acesso a uma **base de dados SQL do Azure**. Se tem de configurar uma base de dados de SQL do Azure, [introdução ao Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) fornece informações sobre como aprovisionar uma nova instância de uma base de dados do SQL do Azure.
* Instalou e configurou **do Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

**Dados**: os processos de migração são demonstrados usando o [conjunto de dados de táxis de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados de táxis de NYC contém informações sobre dados de viagens e fairs e está disponível no armazenamento de Blobs do Azure: [dados de táxis de NYC](https://www.andresmh.com/nyctaxitrips/). Um exemplo e uma descrição destes ficheiros são fornecidos na [descrição de conjunto de dados de viagens de táxis de NYC](sql-walkthrough.md#dataset).

Pode adaptar os procedimentos descritos aqui a um conjunto de seus próprios dados ou siga os passos, conforme descrito usando o conjunto de dados de táxis de NYC. Para carregar o conjunto de dados de táxis de NYC para a base de dados do SQL Server no local, siga o procedimento descrito em [dados de importação em massa na base de dados do SQL Server](sql-walkthrough.md#dbload). Estas instruções são para um SQL Server numa máquina Virtual do Azure, mas o procedimento para carregar para o SQL Server no local é o mesmo.

## <a name="file-to-azure-sql-database"></a> Mover dados de uma origem de ficheiro simples para uma base de dados SQL do Azure
Dados em arquivos simples (formato CSV ou TSV) podem ser movidos para uma base de dados SQL do Azure com uma consulta de SQL de inserir em massa.

### <a name="bulk-insert-sql-query"></a> Consulta SQL de inserção em massa
Os passos para o procedimento usando a consulta de SQL de inserir em massa são semelhantes às abordados nas seções para mover dados de uma origem de ficheiro simples para o SQL Server numa VM do Azure. Para obter detalhes, consulte [consulta de SQL de inserir em massa](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Mover dados de SQL Server no local para uma base de dados SQL do Azure
Se a origem de dados é armazenada num servidor de SQL no local, existem várias possibilidades para mover os dados para uma base de dados SQL do Azure:

1. [Exportar para ficheiro simples](#export-flat-file)
2. [Assistente de migração de base de dados SQL](#insert-tables-bcp)
3. [Base de dados back cópia de segurança e restauro](#db-migration)
4. [Azure Data Factory](#adf)

Os passos para os três primeiros são muito semelhantes a essas secções [mover dados para o SQL Server numa máquina virtual do Azure](move-sql-server-virtual-machine.md) que abordam esses mesmos procedimentos. Ligações para as secções apropriadas nesse tópico são fornecidas nas seguintes instruções.

### <a name="export-flat-file"></a>Exportar para ficheiro simples
Os passos para esta exportação para um ficheiro simples são semelhantes às abordados [exportar para ficheiro simples](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Assistente de migração de base de dados SQL
Os passos para utilizar o Assistente de migração de base de dados SQL são semelhantes às abordados [Assistente de migração de base de dados SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Base de dados back cópia de segurança e restauro
Os passos para utilizar a base de dados, criar cópias de segurança e restauro são semelhantes às abordados [da base de dados back cópia de segurança e restaurar](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Fábrica de dados do Azure
O procedimento para mover dados para uma base de dados SQL do Azure com o Azure Data Factory (ADF) é fornecido no tópico [mover dados de um servidor SQL no local para o SQL Azure com o Azure Data Factory](move-sql-azure-adf.md). Este tópico mostra como mover dados de uma base de dados do SQL Server no local para uma base de dados SQL do Azure através do armazenamento de Blobs do Azure com o ADF.

Considere utilizar o ADF quando os dados precisam ser migrados continuamente num cenário híbrido que acede no local e recursos na cloud e quando os dados são transacionados ou tem de ser modificado ou se tiver adicionada a ele quando a ser migradas de lógica de negócios. ADF permite o agendamento e monitorização de tarefas utilizando scripts JSON simples que gerem o movimento de dados periodicamente. ADF tem também outras capacidades como o suporte para operações complexas.
