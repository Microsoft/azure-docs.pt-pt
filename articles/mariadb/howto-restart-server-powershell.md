---
title: Reiniciar servidor - Azure PowerShell - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para servidor MariaDB utilizando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.openlocfilehash: 1cf5cf3f34f2986bbc18b5293dad4862c24f47a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121083"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Reinicie a base de dados do Azure para o servidor MariaDB utilizando o PowerShell

Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor MariaDB. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem durante a operação.

O reinício do servidor está bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MariaDB. Para reduzir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell está geralmente disponível, torna-se parte de futuros lançamentos do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma base de dados Azure para servidor MariaDB utilizando PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)
