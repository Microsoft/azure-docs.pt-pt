---
title: Versões suportadas - Base de Dados Azure para MySQL
description: Saiba quais as versões do servidor MySQL suportadas no serviço Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751033"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Banco de dados Azure suportado para versões de servidor MySQL

A Azure Database for MySQL foi desenvolvida a partir da [MySQL Community Edition,](https://www.mysql.com/products/community/)utilizando o motor InnoDB.

MySQL usa o esquema de nomeação X.Y.Z. X é a versão principal, Y é a versão menor, e Z é o lançamento da correção de erro. Para obter mais informações sobre o esquema, consulte a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL.

A Azure Database for MySQL suporta atualmente as seguintes versões:

## <a name="mysql-version-56"></a>MySQL Versão 5.6

Desbloqueio de correção de erros: 5.6.47

Consulte as [notas de lançamento](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-57"></a>MySQL Versão 5.7

Desbloqueio de correção de erros: 5.7.29

Consulte as [notas de lançamento](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-80"></a>MySQL Versão 8.0

Desbloqueio de correção de erro: 8.0.15

Consulte as [notas de lançamento](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerir atualizações e atualizações
O serviço gere automaticamente a correção para atualizações de versões de correção de erros. Por exemplo, 5.7.20 a 5.7.21.  

A atualização da versão principal é atualmente suportada pelo serviço para upgrades do MySQL v5.6 para v5.7. Para mais detalhes, consulte [como realizar as principais atualizações de versão](how-to-major-version-upgrade.md). Se quiser fazer upgrade de 5.7 para 8.0, recomendamos que realize [despejo e restaure](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a política de versão Azure Database para a versão MySQL, consulte [este documento](concepts-version-policy.md).
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis de serviço](./concepts-pricing-tiers.md)
