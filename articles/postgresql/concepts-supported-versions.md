---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve as versões pósgresas suportadas e pequenas na Base de Dados Azure para PostgreSQL - Single Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f0c9a01e43f12e8fbe597c85c22b79c0994305e3
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938873"
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
O projeto PostgreSQL emite regularmente lançamentos menores para corrigir bugs reportados. A Base de Dados do Azure para PostgreSQL corrige automaticamente os servidores com versões secundárias durante as implementações mensais do serviço. 

As atualizações automáticas para as principais versões não são suportadas. Para fazer upgrade para uma versão maior, pode 
   * Utilize um dos métodos documentados nas [principais atualizações de versão utilizando o despejo e o restauro](./how-to-upgrade-using-dump-and-restore.md).
   * Utilize [pg_dump e pg_restore](./howto-migrate-using-dump-and-restore.md) para mover uma base de dados para um servidor criado com a nova versão do motor.
   * Utilize [o serviço de migração da Base de Dados Azure](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) para fazer upgrades online.

### <a name="version-syntax"></a>Sintaxe da versão
Antes da versão 10 do PostgreSQL, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão importante_ como um aumento no primeiro _ou_ segundo número. Por exemplo, 9.5 a 9.6 foi considerado uma _grande_ atualização de versão. A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização de versão. Por exemplo, 10.0 a 10.1 é uma _pequena_ atualização de lançamento. A versão 10 a 11 é uma _grande_ atualização de versão.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre extensões postgresQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
