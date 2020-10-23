---
title: Configurar parâmetros do servidor - Azure PowerShell - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para MariaDB usando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d116bdec2974fa2c39c6a56364e54ca7d776b682
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426042"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Configurar parâmetros de servidor na Base de Dados Azure para MariaDB usando PowerShell

Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor MariaDB utilizando o PowerShell. Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente ao nível do servidor ao utilizar a [CLI do Azure](./howto-configure-server-parameters-cli.md), o [PowerShell](./howto-configure-server-parameters-using-powershell.md) ou o [portal do Azure](./howto-server-parameters.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell está geralmente disponível, torna-se parte de futuros lançamentos do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Listar os parâmetros de configuração do servidor para Azure Database para o servidor MariaDB

Para listar todos os parâmetros modificáveis num servidor e os seus valores, execute o `Get-AzMariaDbConfiguration` cmdlet.

O exemplo a seguir lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para a definição de cada um dos parâmetros listados, consulte a secção de referência MariaDB nas [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, executar o `Get-AzMariaDbConfiguration` cmdlet e especificar o parâmetro **Nome.**

Este exemplo mostra detalhes do parâmetro de configuração do servidor ** \_ de \_ registo** de consulta lenta para **o mydemoserver** do servidor sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor

Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor MariaDB. Para atualizar a configuração, utilize o `Update-AzMariaDbConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor de ** \_ \_ registo** de consulta lenta do **servidor mydemoserver** sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Armazenamento de crescimento automático na Base de Dados Azure para servidor MariaDB utilizando PowerShell](howto-auto-grow-storage-powershell.md).