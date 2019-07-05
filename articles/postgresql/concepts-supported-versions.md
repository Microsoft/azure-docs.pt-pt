---
title: Versões suportadas na base de dados do Azure para PostgreSQL - servidor único
description: Descreve as versões suportadas na base de dados do Azure para PostgreSQL - único servidor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: c328f0dc510c1b7d36b547e6560a292f98d72f6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448044"
---
# <a name="supported-postgresql-database-versions"></a>Versões suportadas do banco de dados PostgreSQL
Tem como objetivo da Microsoft para suportar n-2 versões do mecanismo do PostgreSQL na base de dados do Azure para PostgreSQL – único servidor. As versões seria a versão principal atual no Azure (n) e as dois principais as versões anteriores (-2).

Base de dados do Azure para PostgreSQL, atualmente, suporta as seguintes versões:

## <a name="postgresql-version-112"></a>Versão do PostgreSQL 11.2
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) para saber mais sobre as melhorias e correções nesta versão secundária.

>[!NOTE]
> Versão 11 do PostgreSQL está disponível em pré-visualização. Suporte para a criação com o portal do Azure está a ser lançado e talvez ainda não estar disponível na sua região. Pode utilizar o [CLI do Azure](quickstart-create-server-database-azure-cli.md) para criar um servidor de 11 de Postgres em qualquer região. Por exemplo, `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

## <a name="postgresql-version-107"></a>Versão do PostgreSQL 10.7
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9612"></a>Versão do PostgreSQL 9.6.12
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9516"></a>Versão do PostgreSQL 9.5.16
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e atualizações
Base de dados do Azure para PostgreSQL gere automaticamente os patches de versão secundária. Atualmente, a atualização de versão principal não é suportada. Por exemplo, a atualização do PostgreSQL 9,5 para o PostgreSQL 9.6 não é suportada. Se gostaria de atualizar para a próxima versão principal, crie uma base de dados [capturar e restaurar](./howto-migrate-using-dump-and-restore.md) -lo para um servidor que foi criado com a nova versão do motor.

> Observe que, antes de PostgreSQL versão 10, o [política de controlo de versões do PostgreSQL](https://www.postgresql.org/support/versioning/) considerado um _versão principal_ atualização para ser um aumento na primeira _ou_ segundo número (para exemplo, 9.5 para 9.6 foi considerada uma _principais_ atualização da versão).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal (por exemplo, 10.0 para 10.1 é um _pequenas_ atualização da versão e 10 a 11 é uma _principais_ atualização da versão).

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o suporte de extensões do PostgreSQL diferentes, consulte [extensões do PostgreSQL](concepts-extensions.md).
