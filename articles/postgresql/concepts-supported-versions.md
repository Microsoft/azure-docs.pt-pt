---
title: Versões suportadas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve as versões principais e menores suportadas do Poste PostgresQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792230"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais postgresql suportadas
A Microsoft pretende suportar versões n-2 do motor PostgreSQL na Base de Dados Azure para PostgreSQL - Servidor Único. As versões seriam a versão principal atual no Azure (n) e nas duas versões principais anteriores (-2).

A Base de Dados Azure para PostgreSQL suporta atualmente as seguintes versões principais:

## <a name="postgresql-version-11"></a>Versão PostgreSQL 11
A libertação menor atual é de 11,5. Consulte a [documentação postgresQL](https://www.postgresql.org/docs/11/static/release-11-5.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10"></a>PostgreSQL versão 10
A libertação menor atual é de 10.10. Consulte a [documentação postgresQL](https://www.postgresql.org/docs/10/static/release-10-10.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-96"></a>PostgreSQL versão 9.6
A libertação menor atual é de 9.6.15. Consulte a [documentação postgresQL](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-95"></a>Versão PostgreSQL 9.5
A libertação menor atual é de 9.5.19. Consulte a [documentação postgresQL](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) para saber sobre melhorias e correções nesta versão menor.

## <a name="managing-upgrades"></a>Gestão de upgrades
O projeto PostgreSQL emite regularmente pequenas libertações para corrigir bugs reportados. A Base de Dados Azure para PostgreSQL patches automaticamente servidores com pequenas versões durante as implementações mensais do serviço. 

A atualização automática da versão principal não é suportada. Por exemplo, não há nenhuma atualização automática do PostgreSQL 9.5 para o PostgreSQL 9.6. Para atualizar para a próxima versão principal, crie [uma captura e um restauro da base de dados](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a versão do novo motor.

### <a name="version-syntax"></a>Sintaxe de versão
Antes da versão 10 do PostgreSQL, a política de [versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou que uma _grande atualização_ da versão era um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9.6 foi considerado uma _grande_ atualização da versão. A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização da versão. Por exemplo, 10,0 a 10.1 é uma _pequena_ atualização de lançamento. A versão 10 a 11 é uma _grande_ atualização da versão.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre extensões PostgreSQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
