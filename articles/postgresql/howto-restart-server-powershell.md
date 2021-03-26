---
title: Reiniciar servidor - Azure PowerShell - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL utilizando o PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2eadde4e3158c60685ba30542845f28954651bde
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609331"
---
# <a name="restart-azure-database-for-postgresql-server-using-powershell"></a>Reinicie a base de dados do Azure para o servidor PostgreSQL utilizando o PowerShell

Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem durante a operação.

O reinício do servidor está bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

> [!NOTE] 
> O tempo necessário para concluir um reinício depende do processo de recuperação postgreSQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício. Também pode querer aumentar a frequência de verificação. Também pode sintonizar os valores dos parâmetros relacionados com o checkpoint, incluindo `max_wal_size` . Recomenda-se também executar `CHECKPOINT` o comando antes de reiniciar o servidor.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Uma vez que o módulo Az.PostgreSql PowerShell está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurepowershell-interactive
Restart-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de Azure para servidor PostgreSQL utilizando PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)