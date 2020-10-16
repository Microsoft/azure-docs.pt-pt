---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Descreve as versões pósgresas suportadas e pequenas na Base de Dados Azure para PostgreSQL - Servidor Flexível.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936970"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Versões postgresQL suportadas na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A Azure Database for PostgreSQL - Flexible Server suporta atualmente as seguintes principais versões:

## <a name="postgresql-version-12"></a>Versão PostgreSQL 12

A libertação menor atual é 12.1. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-1.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-11"></a>Versão 11 do PostgreSQL

A libertação menor atual é de 11,8. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-8.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10-and-older"></a>Versão Pós-SQL 10 ou mais

Não suportamos a versão 10 do PostgreSQL para Azure Database para PostgreSQL - Servidor Flexível. Por favor, utilize a opção de implementação [do Servidor Único](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) se necessitar de versões mais antigas.

## <a name="managing-upgrades"></a>Gerir upgrades

O projeto PostgreSQL emite regularmente lançamentos menores para corrigir bugs reportados. A Azure Database for PostgreSQL corrige automaticamente servidores com versões menores durante as implementações mensais do serviço.

A atualização automática da versão principal ainda não está suportada. Por exemplo, não existe atualmente nenhuma atualização automática de PostgreSQL 11 para PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
