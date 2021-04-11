---
title: Versões suportadas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Versões PostgreSQL disponíveis na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023992"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Versões de base de dados suportadas na Base de Dados Azure para PostgreSQL – Hyperscale (Citus)

## <a name="postgresql-versions"></a>Versões do PostgreSQL

> [!IMPORTANT]
> As versões PostgreSQL personalizáveis em Hyperscale (Citus) estão atualmente em pré-visualização.  Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

A versão do PostgreSQL a funcionar num grupo de servidores de Hiperescala (Citus) é personalizável durante a criação. Escolher outra coisa que não a versão 11 é atualmente uma funcionalidade de pré-visualização.

A Hiperescala (Citus) suporta atualmente as seguintes principais versões:

### <a name="postgresql-version-13-preview"></a>Versão PostgreSQL 13 (pré-visualização)

A libertação menor atual é de 13,2. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html) para saber mais sobre melhorias e correções nesta versão menor.

### <a name="postgresql-version-12-preview"></a>Versão PostgreSQL 12 (pré-visualização)

A libertação menor atual é de 12,6. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html) para saber mais sobre melhorias e correções nesta versão menor.

### <a name="postgresql-version-11"></a>Versão 11 do PostgreSQL

A libertação menor atual é 11.11. Consulte a [documentação PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) para saber mais sobre melhorias e correções nesta versão menor.

### <a name="postgresql-version-10-and-older"></a>Versão Pós-SQL 10 ou mais

Não suportamos a versão 10 do PostgreSQL para a Base de Dados Azure para PostgreSQL - Hyperscale (Citus).

## <a name="citus-and-other-extension-versions"></a>Citus e outras versões de extensão

Dependendo da versão do PostgreSQL estar a ser executada num grupo de servidores, também serão [instaladas diferentes versões de extensões Postgres.](concepts-hyperscale-extensions.md)  Em particular, Postgres 13 vem com Citus 10, e as versões postgres anteriores vêm com Citus 9.5.

## <a name="next-steps"></a>Passos seguintes

* Veja quais [as extensões instaladas](concepts-hyperscale-extensions.md) em que versões.
* Aprenda a [criar um grupo de servidores Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)
