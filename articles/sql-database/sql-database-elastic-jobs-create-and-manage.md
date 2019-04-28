---
title: Gerir grupos de bases de dados SQL do Azure | Documentos da Microsoft
description: Instruções sobre a criação e gestão de uma tarefa elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: a95b62ab8f639ad38ee3ac9ace4f30b62bd852bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476070"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Criar e gerir aumentados horizontalmente de bases de dados SQL do Azure com tarefas elásticas (pré-visualização)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Tarefas de base de dados elásticas** simplificar a gestão de grupos de bases de dados ao executar operações administrativas, como as alterações de esquema, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou telemetria do inquilino (cliente) coleção. Tarefas elásticas da base de dados está atualmente disponível através do portal do Azure e cmdlets do PowerShell. No entanto, as superfícies de portais do Azure reduzido funcionalidade limitada para execução em todas as bases de dados num [conjunto elástico](sql-database-elastic-pool.md). Para acessar recursos adicionais e execução de scripts num grupo de bases de dados incluindo um conjunto personalizado ou de uma partição horizontal defina (criado usando [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-scale-introduction.md)), consulte [criando e gerenciando as tarefas com o PowerShell](sql-database-elastic-jobs-powershell.md). Para obter mais informações sobre as tarefas, consulte [descrição geral das tarefas de bases de dados elásticas](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Para uma avaliação gratuita, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um conjunto elástico. Ver [sobre os conjuntos elásticos](sql-database-elastic-pool.md).
* Instalação de componentes do serviço de tarefa de bases de dados elásticas. Ver [instalar o serviço de tarefa de bases de dados elásticas](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Criar tarefas
1. Utilizar o [portal do Azure](https://portal.azure.com), a partir de um conjunto de trabalho de bases de dados elásticas existente, clique em **criar tarefa**.
2. Escreva o nome de utilizador e palavra-passe do administrador da base de dados (criado durante a instalação de tarefas) para a base de dados de controlo de tarefas (armazenamento de metadados para as tarefas).
   
    ![Nome da tarefa, escreva ou cole no código e clique em executar][1]
3. Na **criar tarefa** painel, escreva um nome para a tarefa.
4. Escreva o nome de utilizador e palavra-passe para ligar a bases de dados de destino com permissões suficientes para a execução do script com êxito.
5. Cole ou introduza o script T-SQL.
6. Clique em **salvar** e, em seguida, clique em **executar**.
   
    ![Criar tarefas e executar][5]

## <a name="run-idempotent-jobs"></a>Executar tarefas de idempotentes
Quando executa um script com um conjunto de bases de dados, tem de ser-se de que o script é idempotent. Ou seja, o script tem de ser capaz de executar várias vezes, mesmo se tiver falhado antes num estado incompleto. Por exemplo, um script falha, a tarefa será automaticamente repetida até ter êxito (dentro dos limites, como a repetição lógica, eventualmente, deixará da repetir). A maneira de fazer isso é usar a uma cláusula "IF existe" e eliminar qualquer instância encontrada antes de criar um novo objeto. Um exemplo é mostrado aqui:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Em alternativa, utilize uma cláusula "Se não existe" antes de criar uma nova instância:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Este script, em seguida, atualiza a tabela criada anteriormente.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>A verificar o estado de tarefa
Depois de uma tarefa foi iniciada, pode verificar seu andamento.

1. Na página conjunto elástico, clique em **gerir tarefas**.
   
    ![Clique em "Gerir trabalhos"][2]
2. Clique no nome (a) de uma tarefa. O **estado** pode ser "Concluído" ou "Falha". Detalhes da tarefa aparecem (b) com a data e hora da criação e execução. A lista (c) abaixo o que mostra o progresso do script em cada banco de dados no agrupamento, fornecendo os detalhes de data e hora.
   
    ![A verificação de uma tarefa concluída][3]

## <a name="checking-failed-jobs"></a>A verificação de tarefas falhadas
Se uma tarefa falhar, pode encontrar um registo de sua execução. Clique no nome da tarefa falhada para ver os respetivos detalhes.

![Consulte uma tarefa falhada][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


