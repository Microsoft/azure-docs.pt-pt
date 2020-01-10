---
title: Versões com suporte – banco de dados do Azure para MySQL
description: Saiba quais versões do MySQL Server têm suporte no banco de dados do Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 05d4ecd58f6febff75212f1ad88b60be4f23c2a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454324"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Banco de dados do Azure para versões do servidor MySQL com suporte

O banco de dados do Azure para MySQL foi desenvolvido no [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X. Y. Z. X é a versão principal, Y é a versão secundária e Z é o lançamento de correção de bug. Para obter mais informações sobre o esquema, consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> No serviço, um gateway é utilizado para redirecionar as ligações para instâncias de servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL.

Atualmente, o banco de dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-56"></a>MySQL versão 5,6

Versão de correção de bug: 5.6.45

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-57"></a>MySQL versão 5,7

Versão de correção de bug: 5.7.27

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-80"></a>MySQL versão 8,0

Versão de correção de bug: 8.0.15

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bugs. Por exemplo, 5.7.20 para 5.7.21.  

Atualmente, não há suporte para as atualizações das versões principais e secundárias. Por exemplo, não há suporte para a atualização do MySQL 5.6 para o MySQL 5.7. Se quiser atualizar da 5.6 para a 5.7, efetue [uma captura e um restauro](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)
