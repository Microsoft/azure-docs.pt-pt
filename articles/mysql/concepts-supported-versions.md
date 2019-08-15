---
title: Versões com suporte no banco de dados do Azure para MySQL
description: Descreve as versões com suporte no banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 28d635dc5042799790d032ef4b46bf28118cb326
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947159"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Banco de dados do Azure para versões do servidor MySQL com suporte

O banco de dados do Azure para MySQL foi desenvolvido no [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X. Y. Z. X é a versão principal, Y é a versão secundária e Z é o lançamento de correção de bug. Para obter mais informações sobre o esquema, consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para instâncias de servidor. Depois que a conexão é estabelecida, o cliente MySQL exibe a versão do MySQL definido no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do MySQL Server, use o `SELECT VERSION();` comando no prompt do MySQL.

Atualmente, o banco de dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-56"></a>MySQL versão 5,6

Versão de correção de bug: 5.6.42

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) do MySQL para saber mais sobre melhorias e correções no MySQL 5.6.42.

## <a name="mysql-version-57"></a>MySQL versão 5,7

Versão de correção de bug: 5.7.24

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) do MySQL para saber mais sobre melhorias e correções no MySQL 5.7.24.

## <a name="mysql-version-80"></a>MySQL versão 8,0

> [!IMPORTANT]
> O MySQL 8,0 está atualmente em versão prévia.

Versão de correção de bug: 8.0.15

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções no MySQL 8.0.15.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bugs. Por exemplo, 5.7.20 para 5.7.21.  

Atualmente, não há suporte para atualizações de versões secundárias e principais. Por exemplo, não há suporte para a atualização do MySQL 5,6 para o MySQL 5,7. Se você quiser atualizar de 5,6 para 5,7, faça um [despejo e restaure](./concepts-migrate-dump-restore.md) -o para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)
