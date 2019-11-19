---
title: Notas de versão do Backup do Microsoft Azure Server v3
description: Este artigo fornece informações sobre os problemas conhecidos e soluções alternativas para o Backup do Microsoft Azure Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172269"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notas de versão do Backup do Microsoft Azure Server

Este artigo fornece os problemas conhecidos e as soluções alternativas para o Backup do Microsoft Azure Server (MABS) v3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Falha de backup e recuperação para cargas de trabalho clusterizadas

**Descrição:** Falha de backup/restauração para fontes de dados clusterizadas, como cluster do Hyper-V ou cluster do SQL (SQL Always On) ou o Exchange no DAG (grupo de disponibilidade de banco de dados) depois de atualizar MABS v2 para MABS v3.

**Solução alternativa:** Para evitar isso, abra SQL Server Management Studio (SSMS)) e execute o seguinte script SQL no banco de BD do DPM:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>A atualização para o MABS v3 falha na localidade russa

**Descrição:** A atualização do MABS v2 para o MABS V3 na localidade russa falha com um código de erro **4387**.

**Solução alternativa:** Execute as seguintes etapas para atualizar para o MABS v3 usando o pacote de instalação do russo:

1. [Faça backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) de seu banco de dados SQL e desinstale o mAbs v2 (opte por manter os dados protegidos durante a desinstalação).
2. Atualize para o SQL 2017 (Enterprise) e desinstale o Reporting como parte da atualização.
3. [Instalar](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) o SQL Server Reporting Services (SSRS).
4. [Instalar](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o SQL Server Management Studio (SSMS).
5. Configure o relatório usando os parâmetros conforme documentado na [configuração do SSRS com o SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Instalar](backup-azure-microsoft-azure-backup.md) o MABS V3.
7. [Restaurar](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL usando o SSMS e execute a ferramenta de sincronização do DPM, conforme descrito [aqui](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8. Atualize a propriedade ' DataBaseVersion ' na tabela dbo. tbl_DLS_GlobalSetting usando o seguinte comando:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Inicie o serviço MSDPM.

## <a name="next-steps"></a>Passos seguintes

[O que há de novo no MABS v3](backup-mabs-whats-new-mabs.md)
