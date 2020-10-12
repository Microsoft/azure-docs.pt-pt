---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve as versões pósgresas suportadas e pequenas na Base de Dados Azure para PostgreSQL - Single Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: cfe4b92dbed69440ee2c07cff758faad7e01293f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707919"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais pós-SQL suportadas
A Microsoft tem como objetivo suportar as versões n-2 do motor PostgreSQL na Base de Dados Azure para PostgreSQL - Single Server. As versões seriam a versão principal atual em Azure (n) e as duas versões principais anteriores (-2).

A Azure Database for PostgreSQL suporta atualmente as seguintes principais versões:

## <a name="postgresql-version-11"></a>Versão 11 do PostgreSQL
A libertação menor atual é de 11,6. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10"></a>Versão PostgreSQL 10
A libertação menor atual é 10.11. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-96"></a>Versão 9.6 do PostgreSQL
O lançamento atual é de 9.6.16. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-95"></a>Versão 9.5 do PostgreSQL
O lançamento atual é de 9.5.20. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) para saber sobre melhorias e correções nesta versão menor.

## <a name="managing-upgrades"></a>Gerir upgrades
O projeto PostgreSQL emite regularmente lançamentos menores para corrigir bugs reportados. A Azure Database for PostgreSQL corrige automaticamente servidores com versões menores durante as implementações mensais do serviço. 

As atualizações automáticas para as principais versões não são suportadas. Para atualizar para a próxima versão principal, você pode 
   * Utilize [pg_dump e pg_restore](./howto-migrate-using-dump-and-restore.md) para mover uma base de dados para um servidor criado com a nova versão do motor
   * Em alternativa, pode fazer upgrade de PostgreSQL 10 a 11 utilizando o [serviço de migração da Base de Dados Azure](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Sintaxe da versão
Antes da versão 10 do PostgreSQL, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão importante_ como um aumento no primeiro _ou_ segundo número. Por exemplo, 9.5 a 9.6 foi considerado uma _grande_ atualização de versão. A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização de versão. Por exemplo, 10.0 a 10.1 é uma _pequena_ atualização de lançamento. A versão 10 a 11 é uma _grande_ atualização de versão.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre extensões postgresQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
