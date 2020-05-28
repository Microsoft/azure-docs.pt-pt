---
title: Configure parâmetros de servidor - Azure PowerShell - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para MariaDB usando powerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: d7a6f87b704a2e366e0a4e1fc647e83ed88c486c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050857"
---
# <a name="customize-azure-database-for-mariadb-server-parameters-using-powershell"></a>Personalize a Base de Dados Azure para parâmetros do servidor MariaDB utilizando o PowerShell

Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor MariaDB utilizando o PowerShell. Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa a partir de dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista de parâmetros de configuração do servidor para base de dados Azure para servidor MariaDB

Para enumerar todos os parâmetros modificáveis num servidor e seus valores, executar o `Get-AzMariaDbConfiguration` cmdlet.

O exemplo seguinte lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para a definição de cada um dos parâmetros listados, consulte a secção de referência MariaDB nas [Variáveis do Sistema de Servidores](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o `Get-AzMariaDbConfiguration` cmdlet e especifique o parâmetro **nome.**

Este exemplo mostra detalhes do parâmetro de configuração do servidor de ** \_ \_ registo** de consulta lenta para **o servidor mydemoserver** sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor

Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor MariaDB. Para atualizar a configuração, utilize o `Update-AzMariaDbConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor de ** \_ \_ registo** de consulta lenta do **servidor mydemoserver** sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> Armazenamento de [cultivo automático em Base de Dados Azure para servidor MariaDB usando PowerShell](howto-auto-grow-storage-powershell.md).
