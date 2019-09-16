---
title: Versões com suporte no banco de dados do Azure para MariaDB
description: Descreve as versões com suporte no banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962926"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Banco de dados do Azure para versões do servidor MariaDB com suporte

O banco de dados do Azure para MariaDB foi desenvolvido a partir do [servidor MariaDB](https://downloads.mariadb.org/)de código-fonte aberto, usando o mecanismo de InnoDB. 

MariaDB usa o esquema de nomenclatura X. Y. Z. X é a versão principal, Y é a versão secundária e Z é a versão do patch.

> [!NOTE]
> No serviço, é utilizado um gateway para redirecionar as ligações para as instâncias de servidor. Depois que a conexão é estabelecida, o cliente MySQL exibe a versão do MariaDB definido no gateway, não a versão real em execução na instância do servidor MariaDB. Para determinar a versão da sua instância do servidor MariaDB, use `SELECT VERSION();` o comando.

O banco de dados do Azure para MariaDB atualmente dá suporte à seguinte versão:

## <a name="mariadb-version-102"></a>MariaDB versão 10,2

Versão do patch: 10.2.25

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="mariadb-version-103"></a>MariaDB versão 10,3

Versão do patch: 10.3.16

Consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciando atualizações e upgrades
O serviço gerencia automaticamente atualizações para atualizações de patch. Por exemplo, 10.2.21 para 10.2.23.  

Atualmente, não há suporte para atualizações de versões secundárias e principais. Por exemplo, não há suporte para a atualização de MariaDB 10,2 para MariaDB 10,3. Se você quiser atualizar de 10,2 para 10,3, faça um [despejo e restaure](./howto-migrate-dump-restore.md) -o para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre cotas de recursos e limitações específicas com base em sua **camada de serviço**, consulte [camadas de serviço](./concepts-pricing-tiers.md).
