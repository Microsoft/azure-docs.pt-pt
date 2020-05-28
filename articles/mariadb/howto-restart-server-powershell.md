---
title: Servidor restart - Azure PowerShell - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MariaDB utilizando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: ff7a7b7d83089f575df1ac169556c2762dd674a7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050850"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Reiniciar base de dados Azure para servidor MariaDB utilizando powerShell

Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MariaDB. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem durante a operação.

O reinício do servidor está bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como a escala vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MariaDB. Para reduzir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa a partir de dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma Base de Dados Azure para servidor MariaDB usando powerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)
