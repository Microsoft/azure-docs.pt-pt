---
title: Notas de lançamento para Microsoft Azure Backup Server v3
description: Este artigo fornece as informações sobre os problemas e suposições conhecidas para o Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: a5c99bcb95fde39bddc9e9db9ab000881c89081a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185630"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notas de lançamento para o Microsoft Azure Backup Server

Este artigo fornece as questões e suposições conhecidas para o Microsoft Azure Backup Server (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Backup e recuperação falha para cargas de trabalho agrupadas

**Descrição:** Falhas de backup/restauro para fontes de dados agrupadas, tais como cluster Hiper-V ou cluster SQL (SQL Always On) ou Exchange in database availability group (DAG) after upgrade MABS V2 to MABS V3.

**Trabalhar em torno:** Para evitar isto, abra o Estúdio de Gestão de Servidores SQL (SSMS)) e execute o seguinte script SQL no DPM DB:

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

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade para MABS V3 falha no local russo

**Descrição:** O upgrade de MABS V2 para MABS V3 em localidade russa falha com um código de erro **4387**.

**Trabalhar em torno:** Faça os seguintes passos para atualizar para MABS V3 utilizando o pacote de instalação russo:

1. [Faça backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) da base de dados SQL e desinstale o MABS V2 (opte por reter os dados protegidos durante a desinstalação).
2. Upgrade para SQL 2017 (Enterprise) e desinstale relatórios como parte da atualização.
3. [Instalar](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) Serviços de Reporte de Servidores SQL (SSRS).
4. [Instalar](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) Estúdio de Gestão de Servidores SQL (SSMS).
5. Configure relatórios utilizando os parâmetros conforme documentado na [configuração SSRS com SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Instalar](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Restaurar](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL usando SSMS e executar ferramenta DPM-Sync como descrito [aqui](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Atualize a propriedade 'DataBaseVersion' na tabela dbo.tbl_DLS_GlobalSetting utilizando o seguinte comando:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Inicie o serviço MSDPM.

## <a name="next-steps"></a>Passos seguintes

[O que há de novo no MABS V3](backup-mabs-whats-new-mabs.md)
