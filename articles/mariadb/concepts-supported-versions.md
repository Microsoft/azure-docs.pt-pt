---
title: Versões suportadas - Azure Database for MariaDB
description: Saiba quais as versões do servidor MariaDB suportadas na Base de Dados Azure para o serviço MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662445"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Banco de dados de Azure suportado para versões de servidor mariaDB

A Azure Database for MariaDB foi desenvolvida a partir do [open-source MariaDB Server,](https://downloads.mariadb.org/)utilizando o motor InnoDB.

MariaDB usa o esquema de nomeação X.Y.Z. X é a versão principal, Y é a versão menor, e Z é a versão patch.

> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Após a ligação ser estabelecida, o cliente MySQL exibe a versão do Conjunto MariaDB no gateway, e não a versão real em execução na sua instância do servidor MariaDB. Para determinar a versão da sua instância do servidor MariaDB, utilize o `SELECT VERSION();` comando.

A Azure Database for MariaDB suporta atualmente a seguinte versão:

## <a name="mariadb-version-102"></a>MariaDB Versão 10.2

Versão patch: 10.2.32

Consulte a [documentação mariaDB](https://mariadb.com/kb/en/mariadb-10232-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="mariadb-version-103"></a>MariaDB Versão 10.3

Versão patch: 10.3.23

Consulte a [documentação mariaDB](https://mariadb.com/kb/en/mariadb-10323-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerir atualizações e atualizações
O serviço gere automaticamente atualizações para atualizações de patch. Por exemplo, 10.2.21 a 10.2.23.  

Atualmente, não há suporte para as atualizações das versões principais e secundárias. Por exemplo, não há suporte para a atualização do MariaDB 10.2 para o MariaDB 10.3. Se quiser fazer upgrade de 10.2 para 10.3, [despeje-o e restaure-o](./howto-migrate-dump-restore.md) num servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis de Serviço](./concepts-pricing-tiers.md).
