---
title: Armazenamento de cultivo automático - Azure PowerShell - Base de Dados Azure para MySQL
description: Este artigo descreve como pode permitir o armazenamento de cultivo automático usando powerShell em Base de Dados Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 09431a6690be751324bb796415d308d3b7727dad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254099"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-storage-using-powershell"></a>Armazenamento de cultivo automático em Base de Dados Azure para armazenamento MySQL usando PowerShell

Este artigo descreve como pode configurar uma Base de Dados Azure para o armazenamento do servidor MySQL para crescer sem afetar a carga de trabalho.

O cultivo de automóveis de armazenamento impede que o seu servidor [atinja o limite](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) de armazenamento e se torne apenas de leitura. Para servidores com 100 GB ou menos de armazenamento provisionado, o tamanho é aumentado em 5 GB quando o espaço livre é inferior a 10%. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho é aumentado em 5% quando o espaço livre é inferior a 10 GB. Os limites máximos de armazenamento aplicam-se, conforme especificado na secção de armazenamento da Base de Dados Azure para os níveis de [preços MySQL](/azure/mysql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Lembre-se que o armazenamento só pode ser dimensionado para cima, não para baixo.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo `Install-Module -Name Az.MySql -AllowPrerelease`separadamente do módulo Az PowerShell utilizando o seguinte comando: .
> Uma vez que o módulo Az.MySql PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount)

## <a name="enable-mysql-server-storage-auto-grow"></a>Ativar o armazenamento automático do servidor MySQL

Ativar o armazenamento automático do servidor num servidor existente com o seguinte comando:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Ative o armazenamento de crescimento automático do servidor ao criar um novo servidor com o seguinte comando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)