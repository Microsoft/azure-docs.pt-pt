---
title: Gere uma cadeia de conexão com PowerShell - Base de Dados de Azure para PostgreSQL
description: Este artigo fornece um exemplo Azure PowerShell para gerar uma cadeia de ligação para a ligação à Base de Dados Azure para PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708174"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Como gerar uma base de dados Azure para cadeia de conexão PostgreSQL com PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para uma Base de Dados Azure para servidor PostgreSQL. Pode utilizar um fio de ligação para ligar a uma Base de Dados Azure para PostgreSQL a partir de muitas aplicações diferentes.

## <a name="requirements"></a>Requisitos

Este artigo utiliza os recursos criados no seguinte guia como ponto de partida:

* [Quickstart: Criar uma base de dados Azure para servidor PostgreSQL utilizando PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O `Get-AzPostgreSqlConnectionString` cmdlet é utilizado para gerar uma cadeia de ligação para ligar aplicações à Base de Dados Azure para PostgreSQL. O exemplo a seguir devolve a cadeia de ligação a um cliente PHP do **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Os valores válidos para o `Client` parâmetro incluem:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Personalize a Base de Dados de Azure para parâmetros de servidor PostgreSQL utilizando o PowerShell](howto-configure-server-parameters-using-powershell.md)
