---
title: Configurar parâmetros de servidor - Azure PowerShell - Azure Database for MySQL
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para o MySQL utilizando o PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 973025dfd8c0141ed0884539fe5207cc64ec822c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541866"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Configurar parâmetros de servidor na Base de Dados Azure para o MySQL utilizando o PowerShell

Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor MySQL utilizando o PowerShell. Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente ao nível do servidor ao utilizar a [CLI do Azure](./howto-configure-server-parameters-using-cli.md), o [PowerShell](./howto-configure-server-parameters-using-powershell.md) ou o [portal do Azure](./howto-server-parameters.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Uma vez que o módulo Az.MySql PowerShell está geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar os parâmetros de configuração do servidor para Azure Database para o servidor MySQL

Para listar todos os parâmetros modificáveis num servidor e os seus valores, execute o `Get-AzMySqlConfiguration` cmdlet.

O exemplo a seguir lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para a definição de cada um dos parâmetros listados, consulte a secção de referência MySQL nas [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, executar o `Get-AzMySqlConfiguration` cmdlet e especificar o parâmetro **Nome.**

Este exemplo mostra detalhes do parâmetro de configuração do servidor **\_ de \_ registo** de consulta lenta para **o mydemoserver** do servidor sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor

Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor MySQL. Para atualizar a configuração, utilize o `Update-AzMySqlConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor de **\_ \_ registo** de consulta lenta do **servidor mydemoserver** sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Armazenamento de crescimento automático na Base de Dados Azure para servidor MySQL utilizando PowerShell](howto-auto-grow-storage-powershell.md).
