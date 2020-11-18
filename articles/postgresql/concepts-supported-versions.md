---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve as versões pósgresas suportadas e pequenas na Base de Dados Azure para PostgreSQL - Single Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658524"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais pós-SQL suportadas

Consulte [a base de dados Azure para a política de versão postgreSQL](concepts-version-policy.md) para obter detalhes da política de suporte.

A Azure Database for PostgreSQL suporta atualmente as seguintes principais versões:

## <a name="postgresql-version-11"></a>Versão 11 do PostgreSQL
A libertação menor atual é de 11,6. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10"></a>Versão PostgreSQL 10
A libertação menor atual é 10.11. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-96"></a>Versão 9.6 do PostgreSQL
O lançamento atual é de 9.6.16. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-95"></a>Versão 9.5 do PostgreSQL
O lançamento atual é de 9.5.20. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) para saber sobre melhorias e correções nesta versão menor.

> [!NOTE]
> Alinhado com a [política de versão](https://www.postgresql.org/support/versioning/)comunitária de Postgres, a Azure Database for PostgreSQL vai retirar a versão 9.5 do Postgres no dia 11 de fevereiro de 2021. Consulte [a base de dados Azure para obter a política de versão postgreSQL](concepts-version-policy.md) para obter mais detalhes e restrições.

## <a name="managing-upgrades"></a>Gerir upgrades
O projeto PostgreSQL emite regularmente lançamentos menores para corrigir bugs reportados. A Azure Database for PostgreSQL corrige automaticamente servidores com versões menores durante as implementações mensais do serviço. 

As atualizações automáticas para as principais versões não são suportadas. Para atualizar para a próxima versão principal, você pode 
   * Consulte vários métodos para realizar [grandes atualizações de versão usando o despejo e restaurar](./how-to-upgrade-using-dump-and-restore.md)
   * Utilize [pg_dump e pg_restore](./howto-migrate-using-dump-and-restore.md) para mover uma base de dados para um servidor criado com a nova versão do motor
   * Em alternativa, pode fazer upgrade de PostgreSQL 10 a 11 utilizando o [serviço de migração da Base de Dados Azure](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Sintaxe da versão
Antes da versão 10 do PostgreSQL, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão importante_ como um aumento no primeiro _ou_ segundo número. Por exemplo, 9.5 a 9.6 foi considerado uma _grande_ atualização de versão. A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização de versão. Por exemplo, 10.0 a 10.1 é uma _pequena_ atualização de lançamento. A versão 10 a 11 é uma _grande_ atualização de versão.

## <a name="next-steps"></a>Próximos passos
Para obter informações sobre extensões postgresQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
