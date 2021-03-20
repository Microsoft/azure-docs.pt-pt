---
title: Mover dados para uma Base de Dados Azure SQL - Processo de Ciência de Dados de Equipas
description: Mover dados de ficheiros planos (formatos CSV ou TSV) ou de dados armazenados num Servidor SQL para uma Base de Dados SQL Azure.
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
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309517"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para uma Base de Dados SQL do Azure para o Azure Machine Learning

Este artigo descreve as opções de deslocação de dados de ficheiros planos (formatos CSV ou TSV) ou de dados armazenados no SQL Server para uma Base de Dados SQL Azure. Estas tarefas para mover dados para a nuvem fazem parte do Processo de Ciência de Dados da Equipa.

Para um tópico que delineie as opções de deslocação de dados para SQL Server para Machine Learning, consulte [os dados do Move para SQL Server numa máquina virtual Azure](move-sql-server-virtual-machine.md).

A tabela seguinte resume as opções de deslocação de dados para uma Base de Dados Azure SQL.

| <b>FONTE</b> | <b>DESTINO: Base de Dados Azure SQL</b> |
| --- | --- |
| <b>Ficheiro plano (CSV ou TSV formatado)</b> |[Inserir a granel consulta SQL](#bulk-insert-sql-query) |
| <b>Servidor SQL no local</b> |1.[Exportação para Arquivo Plano](#export-flat-file)<br> 2. [Assistente de migração da base de dados SQL](#insert-tables-bcp)<br> 3. [Base de dados de volta e restauro](#db-migration)<br> 4. [Fábrica de Dados Azure](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
Os procedimentos aqui descritos exigem que tenha:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Utilize uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Acesso a uma **Base de Dados Azure SQL**. Se tiver de configurar uma Base de Dados Azure SQL, [começar com a Base de Dados SQL do Microsoft Azure](../../azure-sql/database/single-database-create-quickstart.md) fornece informações sobre como fornecer uma nova instância de uma Base de Dados SQL Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/).

**Dados**: Os processos de migração são demonstrados utilizando o [conjunto de dados do Táxi nyc](https://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados do Táxi nyc contém informações sobre dados de viagem e feiras e está disponível no armazenamento de blob Azure: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Uma amostra e descrição destes ficheiros são fornecidas na Descrição do Conjunto de [Datas de Viagens de Táxi de NYC](sql-walkthrough.md#dataset).

Pode adaptar os procedimentos descritos aqui a um conjunto dos seus próprios dados ou seguir os passos descritos utilizando o conjunto de dados do Táxi de NYC. Para enviar o conjunto de dados do NYC Taxi para a sua base de dados do SQL Server, siga o procedimento descrito em [Dados de Importação a Granel para a Base de Dados do Servidor SQL](sql-walkthrough.md#dbload).

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Mover dados de uma fonte de ficheiro plano para uma Base de Dados Azure SQL
Os dados em ficheiros planos (CSV ou TSV formatados) podem ser transferidos para uma Base de Dados SQL Azure utilizando uma consulta SQL de inserção a granel.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> Inserir a granel consulta SQL
Os passos para o procedimento utilizando a consulta de inserção a granel SQL são semelhantes às instruções para mover dados de uma fonte de ficheiro plana para o SQL Server num VM Azure. Para mais detalhes, consulte [a consulta DE INSERÇÃO A Granel SQL](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> Transferir dados do SQL Server para uma Base de Dados SQL Azure
Se os dados de origem forem armazenados no SQL Server, existem várias possibilidades de mover os dados para uma Base de Dados Azure SQL:

1. [Exportação para Arquivo Plano](#export-flat-file)
2. [Assistente de migração da base de dados SQL](#insert-tables-bcp)
3. [Base de dados de volta e restauro](#db-migration)
4. [Azure Data Factory](#adf)

Os passos para os três primeiros são semelhantes às secções em [Move data para SQL Server em uma máquina virtual Azure](move-sql-server-virtual-machine.md) que cobre estes mesmos procedimentos. As secções adequadas nesse tópico são fornecidas nas seguintes instruções.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportação para Arquivo Plano
Os passos para esta exportação para um ficheiro plano são semelhantes às instruções abrangidas pela [Exportação para o Ficheiro Plano.](move-sql-server-virtual-machine.md#export-flat-file)

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Assistente de migração da base de dados SQL
Os passos para a utilização do Assistente de Migração da Base de Dados SQL são semelhantes às instruções abrangidas pelo [SqL Database Migration Wizard](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Base de dados de volta e restauro
Os passos para a utilização de cópias de segurança e restauro da base de dados são semelhantes às indicações listadas na [cópia de segurança da Base de Dados e restauro](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Saiba como mover dados para uma Base de Dados Azure SQL com Azure Data Factory (ADF) neste tópico, [mover dados de um SqL Server para SQL Azure com Azure Data Factory](move-sql-azure-adf.md). Este tópico mostra como usar a ADF para mover dados de uma base de dados do SQL Server para uma Base de Dados Azure SQL via Azure Blob Storage.

Considere usar a ADF quando os dados precisam de ser continuamente migrados com híbridos no local e fontes de nuvem.  A ADF também ajuda quando os dados precisam de transformações, ou precisa de uma nova lógica de negócio durante a migração. A ADF permite o agendamento e monitorização de postos de trabalho utilizando scripts JSON simples que gerem o movimento de dados numa base periódica. A ADF também tem outras capacidades, como o apoio a operações complexas.