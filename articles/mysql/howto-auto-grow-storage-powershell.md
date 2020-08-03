---
title: Armazenamento de cultivo automático - Azure PowerShell - Base de Dados Azure para MySQL
description: Este artigo descreve como pode permitir o armazenamento de crescimento automático usando PowerShell na Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d355e7557ff38c52872a89941025d33d01bd92d8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503315"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Armazenamento de crescimento automático em Azure Database para servidor MySQL usando PowerShell

Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MySQL crescer sem afetar a carga de trabalho.

O crescimento automático do armazenamento impede que o seu servidor [atinja o limite de armazenamento](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) e se torne apenas de leitura. Para servidores com 100 GB ou menos de armazenamento a provisionado, o tamanho é aumentado em 5 GB quando o espaço livre é inferior a 10%. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho é aumentado em 5% quando o espaço livre é inferior a 10 GB. Os limites máximos de armazenamento aplicam-se conforme especificado na secção de armazenamento da Base de [Dados Azure para os níveis de preços do MySQL](/azure/mysql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Lembre-se que o armazenamento só pode ser aumentado, não para baixo.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Uma vez que o módulo Az.MySql PowerShell está geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount)

## <a name="enable-mysql-server-storage-auto-grow"></a>Ativar o crescimento automático de armazenamento de servidor mySQL

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Ativar o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como criar e gerir réplicas de leitura na Base de Dados Azure para o MySQL utilizando o PowerShell](howto-read-replicas-powershell.md).
