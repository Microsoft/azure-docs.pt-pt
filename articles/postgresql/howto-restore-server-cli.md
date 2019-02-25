---
title: Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para PostgreSQL
description: Saiba como criar cópias de segurança e restaurar um servidor na base de dados do Azure para PostgreSQL com a CLI do Azure.
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: fc874ad04e65a3a80a4ceb76434b997e80019609
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750298"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para PostgreSQL com a CLI do Azure

## <a name="backup-happens-automatically"></a>Cópia de segurança ocorre automaticamente
Base de dados do Azure para servidores PostgreSQL são uma cópia de segurança periodicamente para ativar funcionalidades de restauro. Ao utilizar esta funcionalidade pode restaurar o servidor e todas as suas bases de dados para um anterior ponto anterior no tempo, num servidor novo.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de procedimentos, terá de:
- Um [base de dados do Azure para PostgreSQL server e base de dados](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Este guia de procedimentos é necessário utilizar a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, introduza `az --version`. Para instalar ou atualizar, veja [instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Configuração de conjunto de cópia de segurança

Fazer a escolha entre configurar seu servidor para cópias de segurança localmente redundantes ou cópias de segurança georredundante durante a criação do servidor. 

> [!NOTE]
> Depois de criar um servidor, o tipo de redundância tem, vs geograficamente redundantes localmente redundantes, não pode ser mudado.
>

Durante a criação de um servidor através da `az postgres server create` comando, o `--geo-redundant-backup` parâmetro decide sua opção de redundância da cópia de segurança. Se `Enabled`, as cópias de segurança com redundância geográfica direcionadas. Ou se `Disabled` localmente redundantes backups são feitos. 

O período de retenção de cópia de segurança é definido pelo parâmetro `--backup-retention-days`. 

Para obter mais informações sobre como definir esses valores durante a criação, consulte a [base de dados do Azure para o servidor PostgreSQL início rápido da CLI](quickstart-create-server-database-azure-cli.md).

O período de retenção de cópia de segurança de um servidor pode ser alterado da seguinte forma:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

O exemplo anterior altera o período de retenção de cópia de segurança de mydemoserver para 10 dias.

O período de retenção de cópia de segurança controla até que ponto no tempo que pode ser obtido um restauro de ponto no tempo, uma vez que é baseado em cópias de segurança disponíveis. Restauro de ponto no tempo é descrito mais detalhadamente na secção seguinte.

## <a name="server-point-in-time-restore"></a>Restauro de ponto no tempo do servidor
Pode restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente é deixado como está. Por exemplo, se uma tabela acidentalmente cair no meio-dia hoje em dia, pode restaurar para o tempo, pouco antes do meio-dia. Em seguida, pode obter a tabela em falta e os dados da cópia restaurada do servidor. 

Para restaurar o servidor, utilize a CLI do Azure [restauro do az postgres server](/cli/azure/postgres/server) comando.

### <a name="run-the-restore-command"></a>Execute o comando de restauro

Para restaurar o servidor, no prompt de comando da CLI do Azure, introduza o seguinte comando:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

O `az postgres server restore` comando requer os seguintes parâmetros:
| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos em que o servidor de origem existe.  |
| nome | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecione um ponto anterior no tempo para restaurar para. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu fuso horário local, como `2018-03-13T05:59:00-08:00`. Também pode utilizar o formato UTC Zulu, por exemplo, `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID do servidor de origem do qual pretende restaurar. |

Quando restaurar um servidor para um ponto anterior no tempo, é criado um novo servidor. O servidor original e respetivas bases de dados a partir do ponto no tempo especificado, são copiados para o novo servidor.

A localização e valores de escalão de preço para o servidor restaurado permanecerem o mesmo que o servidor original. 

Depois do processo de restauro estar concluído, localize o novo servidor e certifique-se de que os dados são restaurados conforme esperado.

O novo servidor criado durante um restauro não tem as regras de firewall que existiam no servidor original. Regras de firewall tem de ser configurado separadamente para este servidor novo.

## <a name="geo-restore"></a>Restauro geográfico
Se tiver configurado o seu servidor para cópias de segurança georredundante, um novo servidor de pode ser criado da cópia de segurança desse servidor existente. Este novo servidor de pode ser criado em qualquer região que a base de dados do Azure para PostgreSQL está disponível.  

Para criar um servidor utilizando uma cópia de segurança com redundância geográfica, utilize a CLI do Azure `az postgres server georestore` comando.

> [!NOTE]
> Quando é criado um servidor pode não estar imediatamente disponível para o restauro geográfico. Poderá demorar algumas horas para os metadados necessários ser preenchido.
>

A geo restaurar o servidor, no prompt de comando da CLI do Azure, introduza o seguinte comando:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Este comando cria um novo servidor chamado *mydemoserver georestored* na região E.U.A. Leste que irão pertencer ao *myresourcegroup*. É para fins gerais, servidor de Gen 4 com 8 vCores. O servidor é criado a partir da cópia de segurança georredundante de *mydemoserver*, que também se encontra no grupo de recursos *myresourcegroup*

Se quiser criar o novo servidor no grupo de recursos diferente do servidor existente, em seguida, no `--source-server` parâmetro seria qualificar o nome do servidor como no exemplo seguinte:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

O `az postgres server georestore` comando requer os seguintes parâmetros:
| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|resource-group| myResourceGroup | O nome do grupo de recursos, o novo servidor irá pertencer a.|
|nome | mydemoserver-georestored | O nome do novo servidor. |
|source-server | mydemoserver | O nome do servidor existente, cujas cópias de segurança com redundância geográfica são utilizadas. |
|localização | eualeste | A localização do novo servidor. |
|sku-name| GP_Gen4_8 | Este parâmetro define o escalão de preço, a geração de computação e o número de vCores do novo servidor. GP_Gen4_8 mapeia para fins gerais, servidor de Gen 4 com 8 vCores.|


>[!Important]
>Ao criar um novo servidor por um restauro geográfico, ele herda o mesmo tamanho de armazenamento e o escalão de preço que o servidor de origem. Não não possível alterar estes valores durante a criação. Depois de criar o novo servidor, o tamanho de armazenamento pode ser aumentado.

Depois do processo de restauro estar concluído, localize o novo servidor e certifique-se de que os dados são restaurados conforme esperado.

O novo servidor criado durante um restauro não tem as regras de firewall que existiam no servidor original. Regras de firewall tem de ser configurado separadamente para este servidor novo.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o serviço [cópias de segurança](concepts-backup.md).
- Saiba mais sobre [continuidade do negócio](concepts-business-continuity.md) opções.
