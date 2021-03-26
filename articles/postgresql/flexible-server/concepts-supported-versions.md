---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Descreve as versões pós-QL suportadas em Azure Database for PostgreSQL - Flexible Server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5fda7ebb5a72dd9bbfab0ba72511540cf141563f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608855"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Versões postgresQL suportadas na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A Azure Database for PostgreSQL - Flexible Server suporta atualmente as seguintes principais versões:

## <a name="postgresql-version-12"></a>Versão PostgreSQL 12

A libertação menor atual é de 12,5. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-11"></a>Versão 11 do PostgreSQL

A libertação menor atual é 11.10. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10-and-older"></a>Versão Pós-SQL 10 ou mais

Não suportamos a versão 10 do PostgreSQL para Azure Database para PostgreSQL - Servidor Flexível. Por favor, utilize a opção de implementação [do Servidor Único](../concepts-supported-versions.md) se necessitar de versões mais antigas.

## <a name="managing-upgrades"></a>Gerir upgrades

O projeto PostgreSQL emite regularmente lançamentos menores para corrigir bugs reportados. A Base de Dados do Azure para PostgreSQL corrige automaticamente os servidores com versões secundárias durante as implementações mensais do serviço.

A automatização para a melhora de versão principal ainda não está suportada. Por exemplo, não existe atualmente nenhuma atualização automática de PostgreSQL 11 para PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->