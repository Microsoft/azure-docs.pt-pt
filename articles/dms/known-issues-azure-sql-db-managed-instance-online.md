---
title: Problemas conhecidos e limitações com migrações online para a instância gerenciada do banco de dados SQL do Azure
description: Saiba mais sobre problemas conhecidos/limitações de migração associadas a migrações online para a instância gerenciada do banco de dados SQL do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: bc1cbfc1e86db758a4f4d0240f81f5363f817312
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483144"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Problemas conhecidos/limitações de migração com migrações online para a instância gerenciada do banco de dados SQL do Azure

Os problemas conhecidos e as limitações associadas a migrações online do SQL Server para a instância gerenciada do banco de dados SQL do Azure são descritos abaixo.

> [!IMPORTANT]
> Com as migrações online do SQL Server para o banco de dados SQL do Azure, não há suporte para a migração de tipos de dado de SQL_variant.

## <a name="backup-requirements"></a>Requisitos de backup

- **Backups com soma de verificação**

    O serviço de migração de banco de dados do Azure usa o método de backup e restauração para migrar seus bancos de dados locais para a instância gerenciada do banco de dados SQL. O serviço de migração de banco de dados do Azure só dá suporte a backups criados usando checksum

    [Habilitar ou desabilitar somas de verificação de backup durante backup ou restauração (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Se você levar os backups de banco de dados com compactação, a soma de verificação será um comportamento padrão, a menos que explicitamente desabilitado.

    Com as migrações offline, se você escolher, **deixarei o serviço de migração de banco de dados do Azure...** , o serviço de migração de banco de dados do Azure pegará o backup do banco de dados com a opção checksum habilitada

- **Mídia de backup**

    Certifique-se de fazer cada backup em uma mídia de backup separada (arquivos de backup). O serviço de migração de banco de dados do Azure não dá suporte a backups que são anexados a um único arquivo de backup. Faça backups completos de log e logs para separar arquivos de backup.

## <a name="data-and-log-file-layout"></a>Layout do arquivo de dados e log

- **Número de arquivos de log**

    O serviço de migração de banco de dados do Azure não dá suporte a bancos de dados com vários arquivos de log. Se você tiver vários arquivos de log, reduza-os e reorganize-os em um único arquivo de log de transações. Como não é possível realizar o log remoto de arquivos que não estão vazios, você precisa fazer backup do arquivo de log primeiro.

## <a name="sql-server-features"></a>Recursos de SQL Server

- **FileStream/filetables**

    Atualmente, a instância gerenciada do banco de dados SQL não dá suporte a FileStream e filetables. Para cargas de trabalho dependentes desses recursos, recomendamos que você opte por servidores SQL em execução em VMs do Azure como seu destino do Azure.

- **Tabelas na memória**

    O OLTP na memória está disponível nas camadas Premium e Comercialmente Crítico para a instância gerenciada do banco de dados SQL; a camada de Uso Geral não dá suporte a OLTP na memória.

## <a name="migration-resets"></a>Redefinições de migração

- **Implementações**

    A instância gerenciada do banco de dados SQL é um serviço PaaS com aplicação automática de patches e atualizações de versão. Durante a migração de sua instância gerenciada do banco de dados SQL, as atualizações não críticas são úteis até 36 horas. Posteriormente (e para atualizações críticas), se a migração for interrompida, o processo será redefinido para um estado de restauração completa.

    A transferência de migração só pode ser chamada depois que o backup completo é restaurado e é atualizado com todos os backups de log. Se seus migrações de migração de produção forem afetados, contate o [alias de comentários do Azure DMS](mailto:dmsfeedback@microsoft.com).
