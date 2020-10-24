---
title: Configurar parâmetros de servidor - Azure PowerShell - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como configurar os parâmetros de serviço na Base de Dados Azure para PostgreSQL usando PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24106e40a78d4b5607c352ffd49a310533760a66
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489953"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Personalize a Base de Dados de Azure para parâmetros de servidor PostgreSQL utilizando o PowerShell

Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor PostgreSQL utilizando o PowerShell. Um subconjunto de configurações do motor é exposto ao nível do servidor e pode ser modificado.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Uma vez que o módulo Az.PostgreSql PowerShell está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Listar os parâmetros de configuração do servidor para Azure Database para servidor PostgreSQL

Para listar todos os parâmetros modificáveis num servidor e os seus valores, execute o `Get-AzPostgreSqlConfiguration` cmdlet.

O exemplo a seguir lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para a definição de cada um dos parâmetros listados, consulte a secção de referência PostgreSQL sobre [Variáveis ambientais.](https://www.postgresql.org/docs/12/libpq-envars.html)

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, executar o `Get-AzPostgreSqlConfiguration` cmdlet e especificar o parâmetro **Nome.**

Este exemplo mostra detalhes do parâmetro de configuração do servidor ** \_ de \_ registo** de consulta lenta para **o mydemoserver** do servidor sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor

Também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente ao motor do servidor PostgreSQL. Para atualizar a configuração, utilize o `Update-AzPostgreSqlConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor de ** \_ \_ registo** de consulta lenta do **servidor mydemoserver** sob o grupo de recursos **myresourcegroup**.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Armazenamento de crescimento automático na Base de Dados Azure para servidor PostgreSQL utilizando PowerShell](howto-auto-grow-storage-powershell.md).