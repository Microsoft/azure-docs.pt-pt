---
title: Reiniciar servidor - Azure PowerShell - Base de Dados Azure para o MySQL
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL utilizando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.openlocfilehash: faefdfb93257521c9c687045c1467c5d2d0f84ab
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107942"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Reinicie a base de dados do Azure para o servidor MySQL utilizando o PowerShell

Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem durante a operação.

O reinício do servidor está bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MySQL. Para reduzir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Uma vez que o módulo Az.MySql PowerShell está geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma base de dados Azure para servidor MySQL utilizando PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)
