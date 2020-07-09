---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve as versões pósgresas suportadas e pequenas na Base de Dados Azure para PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 61d9712026daab63a002793adf2e17b9db943f94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976902"
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

A atualização automática da versão principal não é suportada. Por exemplo, não há nenhuma atualização automática do PostgreSQL 9.5 para o PostgreSQL 9.6. Para atualizar para a próxima versão principal, crie [uma captura e um restauro da base de dados](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a versão do novo motor.

### <a name="version-syntax"></a>Sintaxe da versão
Antes da versão 10 do PostgreSQL, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão importante_ como um aumento no primeiro _ou_ segundo número. Por exemplo, 9.5 a 9.6 foi considerado uma _grande_ atualização de versão. A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização de versão. Por exemplo, 10.0 a 10.1 é uma _pequena_ atualização de lançamento. A versão 10 a 11 é uma _grande_ atualização de versão.

## <a name="next-steps"></a>Próximos passos
Para obter informações sobre extensões postgresQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
