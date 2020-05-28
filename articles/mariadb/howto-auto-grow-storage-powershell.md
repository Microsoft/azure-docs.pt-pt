---
title: Armazenamento de cultivo automático - Azure PowerShell - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode permitir o armazenamento de cultivo automático utilizando o PowerShell na Base de Dados Azure para o MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: ac32908fe20424b812487365ccbfd70d74a06b54
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050864"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Armazenamento de cultivo automático em Base de Dados Azure para servidor MariaDB usando PowerShell

Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MariaDB crescer sem afetar a carga de trabalho.

O cultivo de automóveis de armazenamento impede que o seu servidor [atinja o limite](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit) de armazenamento e se torne apenas de leitura. Para servidores com 100 GB ou menos de armazenamento provisionado, o tamanho é aumentado em 5 GB quando o espaço livre é inferior a 10%. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho é aumentado em 5% quando o espaço livre é inferior a 10 GB. Os limites máximos de armazenagem aplicam-se, tal como especificado na secção de armazenamento da Base de Dados Azure para os níveis de [preços MariaDB.](/azure/mariadb/concepts-pricing-tiers#storage)

> [!IMPORTANT]
> Lembre-se que o armazenamento só pode ser dimensionado para cima, não para baixo.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa a partir de dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

## <a name="enable-mariadb-server-storage-auto-grow"></a>Ativar o armazenamento automático do servidor MariaDB

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Ative o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Como criar e gerir réplicas de leitura na Base de Dados Azure para MariaDB utilizando powerShell](howto-read-replicas-powershell.md).
