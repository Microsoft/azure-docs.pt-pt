---
title: Versões suportadas na base de dados do Azure para PostgreSQL
description: Descreve as versões suportadas na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 16064d662c5b101e30f8d2fbb64b39db0848f49f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702314"
---
# <a name="supported-postgresql-database-versions"></a>Versões suportadas do banco de dados PostgreSQL
Tem como objetivo da Microsoft para oferecer suporte a versões n-2 do mecanismo do PostgreSQL na base de dados do Azure para o serviço PostgreSQL. As versões seria a versão principal atual no Azure (n) e as dois principais as versões anteriores (-2).

Base de dados do Azure para PostgreSQL, atualmente, suporta as seguintes versões:

## <a name="postgresql-version-107"></a>Versão do PostgreSQL 10.7
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9612"></a>Versão do PostgreSQL 9.6.12
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="postgresql-version-9516"></a>Versão do PostgreSQL 9.5.16
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) para saber mais sobre as melhorias e correções nesta versão secundária.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e atualizações
Base de dados do Azure para PostgreSQL gere automaticamente os patches de versão secundária. Atualmente, a atualização de versão principal não é suportada. Por exemplo, a atualização do PostgreSQL 9,5 para o PostgreSQL 9.6 não é suportada. Se gostaria de atualizar para a próxima versão principal, crie uma base de dados [capturar e restaurar](./howto-migrate-using-dump-and-restore.md) -lo para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o suporte de extensões do PostgreSQL diferentes, consulte [extensões do PostgreSQL](concepts-extensions.md).
