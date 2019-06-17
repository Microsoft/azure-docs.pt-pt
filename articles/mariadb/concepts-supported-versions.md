---
title: Versões suportadas na base de dados do Azure para MariaDB
description: Descreve as versões suportadas na base de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065727"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Suporte base de dados do Azure para MariaDB versões de servidor

Base de dados do Azure para MariaDB foi desenvolvido a partir de open source [MariaDB servidor](https://downloads.mariadb.org/), usando o mecanismo de InnoDB. Atualmente, a base de dados do Azure para MariaDB suporta a versão seguinte:

## <a name="mariadb-version-102"></a>Versão de MariaDB 10.2

Consulte a [MariaDB documentação](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) para obter mais informações sobre melhorias e correções MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>Versão de MariaDB 10.3

Consulte a [MariaDB documentação](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) para obter mais informações sobre melhorias e correções MariaDB 10.3.14.

> [!NOTE]
> No serviço, um gateway é utilizado para redirecionar as ligações para instâncias de servidor. Depois da ligação é estabelecida, o cliente do MySQL apresenta a versão do MariaDB definida no gateway, não a versão real em execução na sua instância de servidor MariaDB. Para determinar a versão da sua instância de servidor MariaDB, utilize o `SELECT VERSION();` comando na linha de comandos MySQL.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e atualizações

O serviço gere automaticamente a aplicação de patches para as atualizações de versão secundária.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre recursos específicos quotas e limitações com base no seu **escalão de serviço**, consulte [escalões de serviço](./concepts-pricing-tiers.md).
