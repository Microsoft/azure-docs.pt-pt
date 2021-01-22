---
title: Gere uma cadeia de conexão com PowerShell - Base de Dados Azure para MariaDB
description: Este artigo fornece um exemplo Azure PowerShell para gerar uma cadeia de ligação para a ligação à Base de Dados Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 79109a7f39debfa14c8a11d9014685382151ab3b
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663728"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Como gerar uma base de dados Azure para cadeia de ligação MariaDB com PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para uma Base de Dados Azure para servidor MariaDB. Pode utilizar um fio de ligação para ligar a uma Base de Dados Azure para MariaDB a partir de várias aplicações diferentes.

## <a name="requirements"></a>Requisitos

Este artigo utiliza os recursos criados no seguinte guia como ponto de partida:

* [Quickstart: Criar uma base de dados Azure para servidor MariaDB utilizando PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O `Get-AzMariaDbConnectionString` cmdlet é utilizado para gerar uma cadeia de ligação para ligar aplicações à Base de Dados Azure para MariaDB. O exemplo a seguir devolve a cadeia de ligação a um cliente PHP do **mydemoserver**.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Personalize a Base de Dados de Azure para parâmetros do servidor MariaDB utilizando o PowerShell](howto-configure-server-parameters-using-powershell.md)
