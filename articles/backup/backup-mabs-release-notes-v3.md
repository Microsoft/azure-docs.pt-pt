---
title: Notas de versão do Microsoft Azure Backup Server v3
description: Este artigo fornece informações sobre os problemas conhecidos e soluções alternativas para MABS v3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: d37245d7eed39ee9d219578db9e0a50d758ba9a2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499703"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notas de versão do Microsoft Azure Backup Server
Este artigo fornece os problemas conhecidos e soluções alternativas para o servidor de cópia de segurança do Azure (MABS) da Microsoft V3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Falha de cópia de segurança e recuperação para cargas de trabalho em cluster

**Descrição:** Falha de cópia de segurança/restauro para origens de dados em cluster, como o cluster Hyper-V ou cluster do SQL (SQL Always On) ou o Exchange no grupo de disponibilidade de base de dados (DAG) após a atualização MABS V2 para MABS V3.

**Solucionar:** Para evitar esta situação, abra o SQL Server Management Studio (SSMS)) e execute o seguinte script SQL na BD do DPM:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Falha de atualização para o MABS V3 na localidade Russa

**Descrição:** Falha de atualização do MABS V2 para o MABS V3 em Russa localidade com um código de erro **4387**.

**Solucionar:** Siga os passos abaixo para atualizar para o MABS V3 usando russo Instalar pacote:

1.  [Cópia de segurança](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) seu SQL da base de dados e desinstalar o MABS V2 (optar por manter os dados protegidos durante a desinstalação).
2.  Atualizar para o SQL 2017 (Corporativo) e desinstalar a relatórios como parte da atualização.
3. [Instalar](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) do SQL Server Reporting Services (SSRS).
4.  [Instalar](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS).
5.  Configurar relatórios utilizando os parâmetros conforme documentado [configuração do SSRS com o SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Instalar](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Restaurar](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL com SSMS e ferramenta de sincronização do DPM de execução, conforme descrito [aqui](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Atualize a propriedade de 'DataBaseVersion' na tabela de dbo.tbl_DLS_GlobalSetting com o seguinte comando:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Inicie o serviço MSDPM.


## <a name="next-steps"></a>Passos Seguintes

[Quais são as novidades da MABS V3](backup-mabs-whats-new-mabs.md)
