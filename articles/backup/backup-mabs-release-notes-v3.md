---
title: Notas de lançamento para Microsoft Azure Backup Server v3
description: Este artigo fornece as informações sobre os problemas e soluções alternativas conhecidas para o Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91254266"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notas de lançamento para Microsoft Azure Backup Server

Este artigo fornece os problemas e soluções alternativas conhecidas para o Microsoft Azure Backup Server (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Falha na cópia de segurança e recuperação para cargas de trabalho agrupadas

**Descrição:** Falhas de backup/restauro para fontes de dados agrupadas, tais como cluster Hyper-V ou cluster SQL (SQL Always On) ou Exchange in database availability group (DAG) depois de atualizar MABS V2 para MABS V3.

**Trabalhe ao redor:** Para evitar isto, abra o SQL Server Management Studio (SSMS)) e execute o seguinte script SQL no DPM DB:

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

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade para MABS V3 falha em localidade russa

**Descrição:** O upgrade de MABS V2 para MABS V3 em localidade russa falha com um código de erro **4387**.

**Trabalhe ao redor:** Faça os seguintes passos para atualizar para MABS V3 utilizando o pacote de instalação russo:

1. [Faça cópia de segurança](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) da sua base de dados SQL e desinstale o MABS V2 (opte por reter os dados protegidos durante a desinstalação).
2. Upgrade para SQL 2017 (Enterprise) e desinstalar relatórios como parte da atualização.
3. [Instalar](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) Serviços de reporte de servidores SQL (SSRS).
4. [Instalar](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Configure Reporte utilizando os parâmetros documentados na [configuração SSRS com SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Instalar](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Restaurar](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL utilizando SSMS e executar DPM-Sync ferramenta como descrito [aqui](/system-center/dpm/back-up-the-dpm-server#using-dpmsync).
8. Atualize a propriedade 'DataBaseVersion' na tabela dbo.tbl_DLS_GlobalSetting utilizando o seguinte comando:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Inicie o serviço MSDPM.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Após a instalação do UR1, os relatórios do MABS não são atualizados com novos ficheiros RDL

**Descrição**: Com a UR1, o problema de formatação do relatório MABS é corrigido com ficheiros RDL atualizados. Os novos ficheiros RDL não são automaticamente substituídos por ficheiros existentes.

**Solução alternativa**: Para substituir os ficheiros RDL, siga os passos abaixo:

1. Na máquina MABS, abra o URL do Portal web dos serviços de reporte sql.
1. No URL do Portal Web, a Pasta DPMReports está presente no formato de **`DPMReports_<GUID>`**

    >[!NOTE]
    >Há sempre apenas uma pasta com esta convenção de nomeação. Se o MABS for atualizado a partir de uma versão anterior, pode haver outra pasta mais antiga também, mas não poderá abri-la.

    ![Pasta DPMReports](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Selecione e abra a **`DPMReports_<GUID>`** pasta. Os ficheiros de relatório individuais serão listados como mostrado abaixo.

    ![Lista de ficheiros de relatórios individuais](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Selecione os ficheiros de relatório que não terminam com **Relatório,** clique à direita na **Opção** e selecione **Gerir**.

    ![Selecione Gerir para ficheiros de relatórios](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Na nova página, selecione a opção **Substituir** os ficheiros por ficheiros de relatório mais recentes.

    Os últimos ficheiros de relatório podem ser encontrados no caminho `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Por exemplo: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Substitua os ficheiros por ficheiros de relatório mais recentes](./media/backup-mabs-release-notes-v3/replace-files.png)

    Depois de substituídos os ficheiros, certifique-se de que o **Nome** e **a Descrição** estão intactos e não estão vazios.

1. Após a substituição dos ficheiros, reinicie os serviços MABS e utilize os ficheiros do relatório.

## <a name="next-steps"></a>Passos seguintes

[O que há de novo no MABS](backup-mabs-whats-new-mabs.md)
