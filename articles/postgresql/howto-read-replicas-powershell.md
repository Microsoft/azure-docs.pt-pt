---
title: Gerir réplicas de leitura - Azure PowerShell - Base de Dados Azure para PostgreSQL
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL usando PowerShell.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 45876c8e176a4f81abfd927b0e2a8df8fc26ac25
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106905"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL usando PowerShell

Neste artigo, aprende a criar e gerir réplicas de leitura no serviço Azure Database for PostgreSQL utilizando o PowerShell. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-powershell"></a>Azure PowerShell

Pode criar e gerir réplicas de leitura utilizando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- O [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) instalado localmente ou [Azure Cloud Shell](https://shell.azure.com/) no navegador
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Uma vez que o módulo Az.PostgreSql PowerShell está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se optar por utilizar o PowerShell localmente, ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores PostgreSQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor principal está num destes níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O `New-AzPostgreSqlServerReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos onde o servidor de réplica é criado.  |
| Name | mydemoreplicaserver | O nome do novo servidor de réplica que é criado. |

Para criar uma réplica de leitura de região cruzada, utilize o parâmetro **Localização.** O exemplo a seguir cria uma réplica na região **oeste dos EUA.**

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md)

Por predefinição, as réplicas de leitura são criadas com a mesma configuração do servidor que o mestre, a menos que o parâmetro **Sku** seja especificado.

> [!NOTE]
> Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores aos do mestre para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-master-server"></a>Lista réplicas para um servidor principal

Para visualizar todas as réplicas de um determinado servidor principal, executar o seguinte comando:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O `Get-AzMariaDReplica` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou identificação do servidor principal. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A eliminação de um servidor de réplicas de leitura pode ser feita executando o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Excluir um servidor principal

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor principal, pode executar o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Reinicie a base de dados do Azure para o servidor PostgreSQL utilizando o PowerShell](howto-restart-server-powershell.md)
