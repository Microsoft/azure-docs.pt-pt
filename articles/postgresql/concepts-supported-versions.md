---
title: Versões com suporte no banco de dados do Azure para PostgreSQL-servidor único
description: Descreve as versões com suporte no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837905"
---
# <a name="supported-postgresql-database-versions"></a>Versões de banco de dados PostgreSQL com suporte
A Microsoft pretende oferecer suporte a versões n-2 do mecanismo PostgreSQL no banco de dados do Azure para PostgreSQL-servidor único. As versões seriam a versão principal atual no Azure (n) e as duas versões principais anteriores (-2).

O banco de dados do Azure para PostgreSQL atualmente dá suporte às seguintes versões principais:

## <a name="postgresql-version-11"></a>PostgreSQL versão 11
A versão secundária atual é 11,4. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-4.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-10"></a>PostgreSQL versão 10
A versão secundária atual é 10,9. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-9.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-96"></a>PostgreSQL versão 9,6
A versão secundária atual é 9.6.14. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-95"></a>PostgreSQL versão 9,5
A versão secundária atual é 9.5.18. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="managing-upgrades"></a>Gerenciando atualizações
O banco de dados do Azure para PostgreSQL gerencia automaticamente atualizações de versão secundárias. 

Não há suporte para a atualização de versão principal automática. Por exemplo, não há uma atualização automática do PostgreSQL 9,5 para PostgreSQL 9,6. Para atualizar para a próxima versão principal, crie um [despejo de banco de dados e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="version-syntax"></a>Sintaxe da versão
Antes do PostgreSQL versão 10, a [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ . A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ . A versão 10 a 11 é uma atualização de versão _principal_ .

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre extensões PostgreSQL com suporte, consulte [o documento extensões](concepts-extensions.md).
