---
title: Versões com suporte no banco de dados do Azure para MySQL
description: Descreve as versões com suporte no banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 25251b617522840412a4868331e155285f64a18c
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962590"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Banco de dados do Azure para versões do servidor MySQL com suporte

O banco de dados do Azure para MySQL foi desenvolvido no [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X. Y. Z. X é a versão principal, Y é a versão secundária e Z é o lançamento de correção de bug. Para obter mais informações sobre o esquema, consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Depois de a ligação ser estabelecida, o cliente MySQL apresenta a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, utilize o comando `SELECT VERSION();` no prompt do MySQL.

Atualmente, o banco de dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-56"></a>MySQL versão 5,6

Versão de correção de bug: 5.6.44

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-44.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-57"></a>MySQL versão 5,7

Versão de correção de bug: 5.7.26

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-80"></a>MySQL versão 8,0

> [!IMPORTANT]
> O MySQL 8,0 está atualmente em versão prévia.

Versão de correção de bug: 8.0.15

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bugs. Por exemplo, 5.7.20 para 5.7.21.  

Atualmente, não há suporte para atualizações de versões secundárias e principais. Por exemplo, não há suporte para a atualização do MySQL 5,6 para o MySQL 5,7. Se você quiser atualizar de 5,6 para 5,7, faça um [despejo e restaure](./concepts-migrate-dump-restore.md) -o para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md)
