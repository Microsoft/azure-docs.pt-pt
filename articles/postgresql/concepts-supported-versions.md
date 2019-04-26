---
title: Versões suportadas na base de dados do Azure para PostgreSQL
description: Descreve as versões suportadas na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 5b06128979bf448a0b85084d5178d9291beb7691
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422070"
---
# <a name="supported-postgresql-database-versions"></a>Versões suportadas do banco de dados PostgreSQL
Tem como objetivo da Microsoft para oferecer suporte a versões n-2 do mecanismo do PostgreSQL na base de dados do Azure para o serviço PostgreSQL. As versões seria a versão principal atual no Azure (n) e as dois principais as versões anteriores (-2).

Base de dados do Azure para PostgreSQL, atualmente, suporta as seguintes versões:

## <a name="postgresql-version-105"></a>Versão do PostgreSQL 10.5
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-5.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9610"></a>Versão do PostgreSQL 9.6.10
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9514"></a>Versão do PostgreSQL 9.5.14
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e atualizações
Base de dados do Azure para PostgreSQL gere automaticamente os patches de versão secundária. Atualmente, a atualização de versão principal não é suportada. Por exemplo, a atualização do PostgreSQL 9,5 para o PostgreSQL 9.6 não é suportada. Se gostaria de atualizar para a próxima versão principal, crie uma base de dados [capturar e restaurar](./howto-migrate-using-dump-and-restore.md) -lo para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o suporte de extensões do PostgreSQL diferentes, consulte [extensões do PostgreSQL](concepts-extensions.md).
