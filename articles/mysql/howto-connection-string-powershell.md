---
title: Gere uma cadeia de conexão com PowerShell - Base de Dados Azure para o MySQL
description: Este artigo fornece um exemplo Azure PowerShell para gerar uma cadeia de ligação para ligar à Base de Dados Azure para o MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9f960d32dcf1f359327dccc01eeb06825cc3a062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541475"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Como gerar uma base de dados Azure para a cadeia de conexão MySQL com PowerShell

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Personalize a Base de Dados de Azure para os parâmetros do servidor MySQL utilizando o PowerShell](howto-configure-server-parameters-using-powershell.md)
