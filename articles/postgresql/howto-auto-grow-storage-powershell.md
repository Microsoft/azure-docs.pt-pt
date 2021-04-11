---
title: Armazenamento de cultivo automático - Azure PowerShell - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como pode permitir o armazenamento de crescimento automático usando PowerShell na Base de Dados Azure para PostgreSQL.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05333aa4a42b821366ea7ad0a564781422fda66a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551055"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Armazenamento de crescimento automático na Base de Dados Azure para servidor PostgreSQL usando PowerShell

Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento de servidores PostgreSQL crescer sem afetar a carga de trabalho.

O crescimento automático do armazenamento impede que o seu servidor [atinja o limite de armazenamento](./concepts-pricing-tiers.md#reaching-the-storage-limit) e se torne apenas de leitura. Para servidores com 100 GB ou menos de armazenamento a provisionado, o tamanho é aumentado em 5 GB quando o espaço livre é inferior a 10%. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho é aumentado em 5% quando o espaço livre é inferior a 10 GB. Os limites máximos de armazenamento aplicam-se conforme especificado na secção de armazenamento da Base de [Dados Azure para os níveis de preços pós-SQL](./concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Lembre-se que o armazenamento só pode ser aumentado, não para baixo.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Uma vez que o módulo Az.PostgreSql PowerShell está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

## <a name="enable-postgresql-server-storage-auto-grow"></a>Permitir o crescimento automático de armazenamento de servidores PostgreSQL

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Ativar o armazenamento de crescimento automático do servidor enquanto cria um novo servidor com o seguinte comando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL utilizando o PowerShell](howto-read-replicas-powershell.md).