---
title: Questões conhecidas e limitações com migrações online para a Base de Dados Azure SQL gerida
description: Conheça questões/limitações de migração conhecidas associadas às migrações online para a Base de Dados Azure SQL gerida.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648670"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Questões conhecidas/limitações de migração com migrações online para a Base de Dados Azure SQL gerida

Questões e limitações conhecidas que estão associadas às migrações on-line do SQL Server para a Base de Dados Azure SQL são descritas abaixo.

> [!IMPORTANT]
> Com as migrações on-line do SQL Server para a Base de Dados SQL Azure, a migração de tipos de dados SQL_variant não é suportada.

## <a name="backup-requirements"></a>Requisitos de backup

- **Backups com cheques**

    O Serviço de Migração de Bases de Dados Azure utiliza o método de backup e restauro para migrar as suas bases de dados no local para a instância gerida pela Base de Dados SQL. O Serviço de Migração de Bases de Dados Azure apenas suporta backups criados através do checksum.

    [Ativar ou desativar verificações de backup durante a cópia de segurança ou restaurar (Servidor SQL)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Se retirar as cópias de segurança da base de dados com compressão, o controlo é um comportamento predefinido a menos que seja explicitamente desativado.

    Com as migrações offline, se **escolher, permitirei**que o Serviço de Migração da Base de Dados Azure... então o Serviço de Migração da Base de Dados Azure leve a base de dados de reserva com a opção checksum ativada.

- **Meios de apoio**

    Certifique-se de que leva todas as cópias de segurança de um meio de backup separado (ficheiros de backup). O Serviço de Migração de Bases de Dados Azure não suporta cópias de segurança anexas a um único ficheiro de backup. Pegue cópias de segurança completas e faça log backups para separar ficheiros de backup.

## <a name="data-and-log-file-layout"></a>Layout de dados e ficheiros de registo

- **Número de ficheiros de registo**

    O Serviço de Migração de Bases de Dados Azure não suporta bases de dados com vários ficheiros de registo. Se tiver vários ficheiros de registo, encolhe-os e reorganize-os num único ficheiro de registo de transações. Como não pode saquear ficheiros que não estão vazios, tem de fazer o registo primeiro.

## <a name="sql-server-features"></a>Características do Servidor SQL

- **FileStream/FileTables**

    A tualmente, a instância gerida pela Base de Dados SQL não suporta fileStream e FileTables. Para cargas de trabalho dependentes destas funcionalidades, recomendamos que opte por Servidores SQL em Execução em VMs Azure como o seu alvo Azure.

- **Tabelas de memória**

    O OLTP em memória está disponível nos níveis Premium e Business Critical para a instância gerida pela Base de Dados SQL; o nível de Propósito Geral não suporta OLTP em memória.

## <a name="migration-resets"></a>Resets migratórios

- **Implementações**

    A instância gerida pela Base de Dados SQL é um serviço PaaS com atualizações automáticas de remendos e versão. Durante a migração da sua base de dados SQL gerida, as atualizações não críticas são de ajuda até 36 horas. Posteriormente (e para atualizações críticas), se a migração for interrompida, o processo repõe-se para um estado de restauro completo.

    A redução da migração só pode ser chamada depois de restaurar a cópia de segurança completa e apanhar todas as cópias de segurança. Se as suas reduções de migração de produção forem afetadas, contacte o [pseudónimo Azure DMS Feedback](mailto:dmsfeedback@microsoft.com).
