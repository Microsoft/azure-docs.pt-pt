---
title: Versões com suporte no banco de dados do Azure para PostgreSQL-servidor único
description: Descreve as versões com suporte no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551381"
---
# <a name="supported-postgresql-database-versions"></a>Versões de banco de dados PostgreSQL com suporte
A Microsoft pretende oferecer suporte a versões n-2 do mecanismo PostgreSQL no banco de dados do Azure para PostgreSQL-servidor único. As versões seriam a versão principal atual no Azure (n) e as duas versões principais anteriores (-2).

O banco de dados do Azure para PostgreSQL atualmente dá suporte às seguintes versões:

## <a name="postgresql-version-112"></a>PostgreSQL versão 11,2
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-107"></a>PostgreSQL versão 10,7
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9612"></a>PostgreSQL versão 9.6.12
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9516"></a>PostgreSQL versão 9.5.16
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades
O banco de dados do Azure para PostgreSQL gerencia automaticamente patches de versão secundária. Atualmente, não há suporte para a atualização da versão principal. Por exemplo, não há suporte para a atualização do PostgreSQL 9,5 para PostgreSQL 9,6. Se você quiser atualizar para a próxima versão principal, crie um despejo de banco de dados [e restaure](./howto-migrate-using-dump-and-restore.md) -o para um servidor que foi criado com a nova versão do mecanismo.

> Observe que, antes do PostgreSQL versão 10, [a política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número (por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ ).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal (por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ e 10 a 11 é uma atualização de versão _principal_ ).

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o suporte de extensões PostgreSQL diferentes, consulte [extensões do PostgreSQL](concepts-extensions.md).
