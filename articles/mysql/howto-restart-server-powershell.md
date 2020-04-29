---
title: Servidor de reiniciar - Azure PowerShell - Base de Dados Azure para MySQL
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL utilizando o PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 3f49ea14d5737e9c55e7193c18a5fc2ac5922ec5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82230863"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Reiniciar base de dados Azure para servidor MySQL usando powerShell

Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem durante a operação.

O reinício do servidor está bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como a escala vCores.

O tempo necessário para completar um reinício depende do processo de recuperação do MySQL. Para reduzir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell estiver em pré-visualização, deve instalá-lo `Install-Module -Name Az.MySql -AllowPrerelease`separadamente do módulo Az PowerShell utilizando o seguinte comando: .
> Uma vez que o módulo Az.MySql PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```
