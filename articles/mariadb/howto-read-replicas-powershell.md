---
title: Gerir réplicas de leitura - Azure PowerShell - Azure Database for MariaDB
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando o PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a5921026c8f260683f091870850350fcc4619f6b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538636"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando PowerShell

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para o serviço MariaDB utilizando o PowerShell. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-powershell"></a>Azure PowerShell

Pode criar e gerir réplicas de leitura utilizando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MariaDb PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Uma vez que o módulo Az.MariaDb PowerShell está geralmente disponível, torna-se parte de futuros lançamentos do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O `New-AzMariaDbServerReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde o servidor de réplica é criado.  |
| Name | mydemoreplicaserver | O nome do novo servidor de réplica que é criado. |

Para criar uma réplica de leitura de região cruzada, utilize o parâmetro **Localização.** O exemplo a seguir cria uma réplica na região **oeste dos EUA.**

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md)

Por predefinição, as réplicas de leitura são criadas com a mesma configuração do servidor que a fonte, a menos que o parâmetro **Sku** seja especificado.

> [!NOTE]
> Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-source-server"></a>Lista réplicas para um servidor de origem

Para visualizar todas as réplicas de um determinado servidor de origem, executar o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O `Get-AzMariaDReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou identificação do servidor de origem. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A eliminação de um servidor de réplicas de leitura pode ser feita executando o `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> Eliminar um servidor de origem interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor de origem. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor de origem, pode executar o `Remove-AzMariaDbServer` cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reinicie a base de dados do Azure para o servidor MariaDB utilizando o PowerShell](howto-restart-server-powershell.md)