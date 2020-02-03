---
title: Mover dados para um Azure SQL Database - Team Data Science Process
description: Mover dados de ficheiros planos (formatos CSV ou TSV) ou de dados armazenados num Servidor SQL no local para uma Base de Dados SQL Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722463"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para uma Base de Dados SQL do Azure para o Azure Machine Learning

Este artigo descreve as opções para mover dados quer a partir de ficheiros planos (formatos CSV ou TSV) quer de dados armazenados num Servidor SQL no local para uma Base de Dados SQL Azure. Estas tarefas para mover dados para a cloud fazem parte do processo de ciência de dados de equipa.

Para um tópico que delineie as opções para mover dados para um Servidor SQL no local para machine learning, consulte [mover dados para o Servidor SQL numa máquina virtual Azure](move-sql-server-virtual-machine.md).

A tabela seguinte resume as opções para mover dados para uma base de dados do SQL do Azure.

| <b>FONTE</b> | <b>DESTINO: Base de Dados Azure SQL</b> |
| --- | --- |
| <b>Ficheiro plano (CSV ou TSV formatado)</b> |[Consulta SQL de inserção a granel](#bulk-insert-sql-query) |
| <b>No local, O Servidor SQL</b> |1.[Exportação para ficheiro plano](#export-flat-file)<br> 2. Assistente de migração de [base de dados SQL](#insert-tables-bcp)<br> 3. [Base de dados de volta e restauro](#db-migration)<br> 4. Fábrica de [Dados Azure](#adf) |

## <a name="prereqs"></a>Pré-requisitos
Os procedimentos descritos aqui exigem que tenha:

* Uma **subscrição Azure.** Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Utilize uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Ver [Gerir as chaves](../../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .
* Acesso a uma Base de **Dados SQL Azure**. Se tiver de configurar uma base de dados Azure SQL, iniciar-se com a Base de [Dados SQL](../../sql-database/sql-database-get-started.md) do Microsoft Azure fornece informações sobre como fornecer uma nova instância de uma base de dados Azure SQL.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

**Dados**: Os processos de migração são demonstrados utilizando o conjunto de dados do [táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados do TÁXI NYC contém informações sobre dados e feiras de viagem e está disponível no armazenamento de blob Azure: [NY Taxi Data](https://www.andresmh.com/nyctaxitrips/). Uma amostra e descrição destes ficheiros são fornecidas na [Descrição](sql-walkthrough.md#dataset)do conjunto de dados de viagens de táxi de NYC .

Pode adaptar os procedimentos descritos aqui a um conjunto de seus próprios dados ou siga os passos, conforme descrito usando o conjunto de dados de táxis de NYC. Para fazer o upload do conjunto de dados do Táxi NYC para a sua base de dados do SQL Server no local, siga o procedimento descrito nos Dados de Importação a granel para a Base de Dados do [Servidor SQL](sql-walkthrough.md#dbload). Estas instruções são para um SQL Server numa máquina Virtual do Azure, mas o procedimento para carregar para o SQL Server no local é o mesmo.

## <a name="file-to-azure-sql-database"></a>Movendo dados de uma fonte de ficheiro plano para uma base de dados Azure SQL
Os dados em ficheiros planos (formado em CSV ou TSV) podem ser transferidos para uma base de dados Azure SQL utilizando uma consulta SQL de inserção a granel.

### <a name="bulk-insert-sql-query"></a>Consulta SQL de inserção a granel
As etapas para o procedimento utilizando a Consulta SQL de Inserção a granel são semelhantes às instruções para mover dados de uma fonte de ficheiro plana para O Servidor SQL num VM Azure. Para mais detalhes, consulte A [Consulta SQL de Inserção a granel](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Movendo dados do servidor SQL no local para uma base de dados Azure SQL
Se os dados de origem forem armazenados num Servidor SQL no local, existem várias possibilidades de transferir os dados para uma Base de Dados Azure SQL:

1. [Exportação para Arquivo Plano](#export-flat-file)
2. [Feiticeiro de Migração de Bases de Dados SQL](#insert-tables-bcp)
3. [Base de dados de volta e restauro](#db-migration)
4. [Azure Data Factory](#adf)

Os passos para os três primeiros são semelhantes aos de [Move dados para o SQL Server numa máquina virtual Azure](move-sql-server-virtual-machine.md) que cobre estes mesmos procedimentos. Ligações para as secções apropriadas nesse tópico são fornecidas nas seguintes instruções.

### <a name="export-flat-file"></a>Exportação para Arquivo Plano
As medidas para esta exportação para um ficheiro plano são semelhantes às direções abrangidas pela [Exportação para o Ficheiro Plano](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Feiticeiro de Migração de Bases de Dados SQL
Os passos para a utilização do Assistente de Migração de Bases de Dados SQL são semelhantes aos indicações cobertos pelo [SQL Database Migration Wizard](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Base de dados de volta e restauro
Os passos para utilizar a cópia de segurança e restauro da base de dados são semelhantes às indicações listadas na cópia de [segurança e restauro](move-sql-server-virtual-machine.md#sql-backup)da base de dados .

### <a name="adf"></a>Fábrica de Dados Azure
Saiba como mover dados para uma Base de Dados Azure SQL com a Azure Data Factory (ADF) neste tópico, [mover dados de um servidor SQL no local para o SQL Azure com a Azure Data Factory](move-sql-azure-adf.md). Este tópico mostra como usar a ADF para mover dados de uma base de dados do SQL Server no local para uma base de dados Azure SQL via Azure Blob Storage.

Considere usar a ADF quando os dados precisam de ser continuamente migrados com fontes híbridas no local e na nuvem.  A ADF também ajuda quando os dados precisam de transformações, ou precisa de uma nova lógica de negócio durante a migração. ADF permite o agendamento e monitorização de tarefas utilizando scripts JSON simples que gerem o movimento de dados periodicamente. ADF tem também outras capacidades como o suporte para operações complexas.
