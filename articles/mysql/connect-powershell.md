---
title: Gere uma cadeia de conexão com PowerShell - Base de Dados Azure para o MySQL
description: Este quickstart fornece um exemplo Azure PowerShell para gerar uma cadeia de ligação para ligar à Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544949"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Base de Dados Azure para MySQL: Gerar uma cadeia de conexão com PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para uma Base de Dados Azure para o servidor MySQL. Pode utilizar um fio de ligação para ligar a uma Base de Dados Azure para o MySQL a partir de muitas aplicações diferentes.

## <a name="requirements"></a>Requisitos

Este artigo utiliza os recursos criados no seguinte guia como ponto de partida:

* [Quickstart: Criar uma base de dados Azure para servidor MySQL utilizando PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O `Get-AzMySqlConnectionString` cmdlet é utilizado para gerar uma cadeia de ligação para ligar aplicações à Base de Dados Azure para o MySQL. O exemplo a seguir devolve a cadeia de ligação a um cliente PHP do **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Os valores válidos para o `Client` parâmetro incluem:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Desenhe uma base de dados Azure para o MySQL utilizando o PowerShell](tutorial-design-database-using-powershell.md)
