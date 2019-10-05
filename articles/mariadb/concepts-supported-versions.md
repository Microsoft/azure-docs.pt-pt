---
title: Versões com suporte – banco de dados do Azure para MariaDB
description: Saiba quais versões do servidor MariaDB têm suporte no banco de dados do Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 950f8600dac6fccfa72f5dea6e900a210572ee38
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973541"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Banco de dados do Azure para versões do servidor MariaDB com suporte

O banco de dados do Azure para MariaDB foi desenvolvido a partir do [servidor MariaDB](https://downloads.mariadb.org/)de código-fonte aberto, usando o mecanismo de InnoDB.

MariaDB usa o esquema de nomenclatura X. Y. Z. X é a versão principal, Y é a versão secundária e Z é a versão do patch.

> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Depois que a conexão é estabelecida, o cliente MySQL exibe a versão do MariaDB definido no gateway, não a versão real em execução na instância do servidor MariaDB. Para determinar a versão da sua instância do servidor MariaDB, use o comando `SELECT VERSION();`.

O banco de dados do Azure para MariaDB atualmente dá suporte à seguinte versão:

## <a name="mariadb-version-102"></a>MariaDB versão 10,2

Versão do patch: 10.2.25

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="mariadb-version-103"></a>MariaDB versão 10,3

Versão do patch: 10.3.16

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades
O serviço gerencia automaticamente atualizações para atualizações de patch. Por exemplo, 10.2.21 para 10.2.23.  

Atualmente, não há suporte para as atualizações das versões principais e secundárias. Por exemplo, não há suporte para a atualização do MariaDB 10.2 para o MariaDB 10.3. Se você quiser atualizar de 10,2 para 10,3, faça um [despejo e restaure](./howto-migrate-dump-restore.md) -o para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre cotas de recursos e limitações específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md).
