---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Descreve as versões pós-QL suportadas em Azure Database for PostgreSQL - Flexible Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542085"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Versões postgresQL suportadas na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A Azure Database for PostgreSQL - Flexible Server suporta atualmente as seguintes principais versões:

## <a name="postgresql-version-12"></a>Versão PostgreSQL 12

A libertação menor atual é de 12,4. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-11"></a>Versão 11 do PostgreSQL

A libertação menor atual é de 11,9. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10-and-older"></a>Versão Pós-SQL 10 ou mais

Não suportamos a versão 10 do PostgreSQL para Azure Database para PostgreSQL - Servidor Flexível. Por favor, utilize a opção de implementação [do Servidor Único](../concepts-supported-versions.md) se necessitar de versões mais antigas.

## <a name="managing-upgrades"></a>Gerir upgrades

O projeto PostgreSQL emite regularmente lançamentos menores para corrigir bugs reportados. A Azure Database for PostgreSQL corrige automaticamente servidores com versões menores durante as implementações mensais do serviço.

A automatização para a melhora de versão principal ainda não está suportada. Por exemplo, não existe atualmente nenhuma atualização automática de PostgreSQL 11 para PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->