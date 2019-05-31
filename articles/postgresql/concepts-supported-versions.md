---
title: Versões suportadas na base de dados do Azure para PostgreSQL - servidor único
description: Descreve as versões suportadas na base de dados do Azure para PostgreSQL - único servidor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4dcbaf159fce6b5f5495a6a25a2a3420cad9e5e8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067266"
---
# <a name="supported-postgresql-database-versions"></a>Versões suportadas do banco de dados PostgreSQL
Tem como objetivo da Microsoft para suportar n-2 versões do mecanismo do PostgreSQL na base de dados do Azure para PostgreSQL – único servidor. As versões seria a versão principal atual no Azure (n) e as dois principais as versões anteriores (-2).

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
