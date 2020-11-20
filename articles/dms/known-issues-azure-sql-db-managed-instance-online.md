---
title: Questões e limitações conhecidas com migrações online para Azure SQL Gestão de Instância
description: Conheça as questões conhecidas/limitações de migração associadas às migrações online para Azure SQL Managed Instance.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 1d5c79a141dbe1310762dc90b447fe78848ac10d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962489"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Questões conhecidas/limitações de migração com migrações on-line para Azure SQL Gestão De Instância

Problemas e limitações conhecidos que estão associados às migrações on-line do SQL Server para Azure SQL Managed Instance são descritos abaixo.

> [!IMPORTANT]
> Com migrações on-line do SQL Server para Azure SQL Database, a migração de SQL_variant tipos de dados não é suportada.

## <a name="backup-requirements"></a>Requisitos de backup

- **Backups com checkum**

    O Serviço de Migração da Base de Dados Azure utiliza o método de backup e restauro para migrar as bases de dados no local para a SQL Managed Instance. O Serviço de Migração da Base de Dados Azure suporta apenas cópias de segurança criadas através do checksum.

    [Ativar ou desativar os custos de verificação de cópias de segurança durante a cópia de segurança ou restauro (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Se pegar nas cópias de segurança da base de dados com compressão, a conta de verificação é um comportamento predefinido, a menos que seja explicitamente desativado.

    Com as migrações offline, se **escolher, permitirei que o Serviço de Migração da Base de Dados de Azure...** então o Serviço de Migração da Base de Dados Azure irá fazer cópias de segurança da base de dados com a opção checkum ativada.

- **Meios de comunicação**

    Certifique-se de que leva todas as cópias de segurança de um meio de cópia de segurança separados (ficheiros de cópia de segurança). O Serviço de Migração da Base de Dados Azure não suporta cópias de segurança que estejam anexadas a um único ficheiro de backup. Pegue a cópia de segurança completa e faça cópias de segurança para separar ficheiros de backup.

## <a name="data-and-log-file-layout"></a>Layout de ficheiros de dados e registo

- **Número de ficheiros de registo**

    O Azure Database Migration Service não suporta bases de dados com vários ficheiros de registo. Se tiver vários ficheiros de registo, encolhe-os e reorganiza-os num único ficheiro de registo de transações. Como não é possível registar ficheiros que não estão vazios, tem de fazer o registo primeiro.

## <a name="sql-server-features"></a>Funcionalidades do SQL Server

- **FileStream/FileTables**

    A sql Managed Instance não suporta fileStream e FileTables. Para cargas de trabalho dependentes destas funcionalidades, recomendamos que opte por SqL Servers que executam em VMs Azure como alvo Azure.

- **Tabelas de memória**

    OLTP em memória está disponível nos níveis Premium e Business Critical para SQL Managed Instance; o nível de Finalidade Geral não suporta OLTP de memória.

## <a name="migration-resets"></a>Resets migratórios

- **Implementações**

    SQL Managed Instance é um serviço PaaS com remendos automáticos e atualizações de versão. Durante a migração da sua SQL Managed Instance, as atualizações não críticas são mantidas por até 36 horas. Posteriormente (e para atualizações críticas), se a migração for interrompida, o processo reinicia para um estado de restauro total.

    O corte de migração só pode ser chamado depois de restaurado o backup completo e recuperar todos os backups de registo. Se os cortes de migração da sua produção forem afetados, contacte o [pseudónimo Azure DMS Feedback](mailto:dmsfeedback@microsoft.com).