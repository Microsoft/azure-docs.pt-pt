---
title: Versões suportadas - Base de Dados Azure para MySQL
description: Saiba quais as versões do servidor MySQL que são suportadas na Base de Dados Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536977"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Base de Dados Azure suportada para versões de servidorMySQL

A Base de Dados Azure para mySQL foi desenvolvida a partir da [MySQL Community Edition,](https://www.mysql.com/products/community/)utilizando o motor InnoDB.

MySQL usa o esquema de nomeação X.Y.Z. X é a versão principal, Y é a versão menor, e Z é o lançamento da correção do bug. Para obter mais informações sobre o esquema, consulte a [documentação MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL.

A Base de Dados Azure para MySQL suporta atualmente as seguintes versões:

## <a name="mysql-version-56"></a>Versão MySQL 5.6

Lançamento da correção do bug: 5.6.45

Consulte as notas de [lançamento](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-57"></a>Versão MySQL 5.7

Lançamento da correção do bug: 5.7.27

Consulte as notas de [lançamento](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-80"></a>Versão MySQL 8.0

Lançamento da correção do bug: 8.0.15

Consulte as notas de [lançamento](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gestão de atualizações e atualizações
O serviço gere automaticamente a correção para atualizações da versão de correção de erros. Por exemplo, 5.7.20 a 5.7.21.  

Atualmente, não há suporte para as atualizações das versões principais e secundárias. Por exemplo, não há suporte para a atualização do MySQL 5.6 para o MySQL 5.7. Se quiser atualizar da 5.6 para a 5.7, efetue [uma captura e um restauro](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis](./concepts-pricing-tiers.md) de serviço
