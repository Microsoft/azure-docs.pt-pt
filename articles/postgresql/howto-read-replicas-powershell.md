---
title: Gerir réplicas de leitura - Azure PowerShell - Base de Dados Azure para PostgreSQL
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL usando PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0a5547928bd7d19343c50e40ab9fcb2c335e893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674536"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL usando PowerShell

Neste artigo, aprende a criar e gerir réplicas de leitura no serviço Azure Database for PostgreSQL utilizando o PowerShell. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-powershell"></a>Azure PowerShell

Pode criar e gerir réplicas de leitura utilizando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Uma vez que o módulo Az.PostgreSql PowerShell está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores PostgreSQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor primário está num destes níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O `New-AzPostgreSqlServerReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde o servidor de réplica é criado.  |
| Name | mydemoreplicaserver | O nome do novo servidor de réplica que é criado. |

Para criar uma réplica de leitura de região cruzada, utilize o parâmetro **Localização.** O exemplo a seguir cria uma réplica na região **oeste dos EUA.**

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSQLServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md)

Por predefinição, as réplicas de leitura são criadas com a mesma configuração do servidor que a principal, a menos que o parâmetro **Sku** seja especificado.

> [!NOTE]
> Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores aos primários para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-primary-server"></a>Lista réplicas para um servidor primário

Para visualizar todas as réplicas de um determinado servidor primário, execute o seguinte comando:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O `Get-AzPostgreSQLReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou identificação do servidor primário. |

### <a name="stop-a-replica-server"></a>Parar um servidor de réplica

Parar um servidor de réplica de leitura promove a réplica de leitura para ser um servidor independente. Pode ser feito executando o `Update-AzPostgreSqlServer` cmdlet e definindo o valor replicationRole para `None` .

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A eliminação de um servidor de réplicas de leitura pode ser feita executando o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Eliminar um servidor primário

> [!IMPORTANT]
> Eliminar um servidor primário para a replicação de todos os servidores de réplicas e elimina o próprio servidor primário. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor primário, pode executar o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reinicie a base de dados do Azure para o servidor PostgreSQL utilizando o PowerShell](howto-restart-server-powershell.md)