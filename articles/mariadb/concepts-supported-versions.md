---
title: Versões suportadas - Base de Dados Azure para MariaDB
description: Saiba quais as versões do servidor MariaDB que são suportadas na Base de Dados Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79527712"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Base de Dados Azure suportada para versões de servidor MariaDB

A Base de Dados Azure para MariaDB foi desenvolvida a partir do [servidor MariaDB](https://downloads.mariadb.org/)de código aberto, utilizando o motor InnoDB.

MariaDB usa o esquema de nomeação X.Y.Z. X é a versão principal, Y é a versão menor, e Z é a versão patch.

> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Após a ligação ser estabelecida, o cliente MySQL exibe a versão do MariaDB definida no gateway, e não a versão real em execução na sua instância de servidor MariaDB. Para determinar a versão da sua instância `SELECT VERSION();` de servidor MariaDB, utilize o comando.

A Base de Dados Azure para MariaDB suporta atualmente a seguinte versão:

## <a name="mariadb-version-102"></a>Versão MariaDB 10.2

Versão patch: 10.2.25

Consulte a [documentação mariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="mariadb-version-103"></a>Versão MariaDB 10.3

Versão patch: 10.3.16

Consulte a [documentação mariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gestão de atualizações e atualizações
O serviço gere automaticamente atualizações para atualizações de patch. Por exemplo, 10.2.21 a 10.2.23.  

Atualmente, não há suporte para as atualizações das versões principais e secundárias. Por exemplo, não há suporte para a atualização do MariaDB 10.2 para o MariaDB 10.3. Se quiser fazer o upgrade de 10.2 para 10.3, [despeje e restaure-o](./howto-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de serviço,** consulte [os níveis](./concepts-pricing-tiers.md)de serviço .
