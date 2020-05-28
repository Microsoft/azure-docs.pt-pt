---
title: Gerir réplicas de leitura - Azure PowerShell - Base de Dados Azure para MariaDB
description: Aprenda a configurar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando powerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: 4854518d77ca3eb0c978a0ca3462535e17ad3fcd
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84052033"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando powerShell

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para o serviço MariaDB utilizando o PowerShell. Para saber mais sobre as réplicas de leitura, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-powershell"></a>Azure PowerShell

Pode criar e gerir réplicas de leitura usando powerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell esteja geralmente disponível, torna-se parte das futuras versões do módulo Az PowerShell e disponível de forma nativa a partir de dentro da Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para base de dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor principal está num destes níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O `New-AzMariaDbServerReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde o servidor de réplica é criado.  |
| Name | mydemoreplicaserver | O nome do novo servidor de réplicas que é criado. |

Para criar uma réplica de leitura de região transversal, utilize o parâmetro **Localização.** O exemplo seguinte cria uma réplica na região **dos EUA Ocidentais.**

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md)

Por predefinição, as réplicas de leitura são criadas com a mesma configuração do servidor que o mestre, a menos que o parâmetro **Sku** seja especificado.

> [!NOTE]
> Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores ao do mestre para garantir que a réplica é capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-master-server"></a>Listar réplicas para um servidor principal

Para ver todas as réplicas para um determinado servidor principal, executar o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O `Get-AzMariaDReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou identificação do servidor principal. |

### <a name="delete-a-replica-server"></a>Eliminar um servidor de réplicas

A apagar um servidor de réplica de leitura pode ser feita executando o `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor principal, pode executar o `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Reiniciar base de dados Azure para servidor MariaDB utilizando powerShell](howto-restart-server-powershell.md)
