---
title: Backup e restauração-CLI do Azure-banco de dados do Azure para MySQL
description: Saiba como fazer backup e restaurar um servidor no banco de dados do Azure para MySQL usando o CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b2458c42a77ffee6985165252b0ebab836fce457
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774188"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Como fazer backup e restaurar um servidor no banco de dados do Azure para MySQL usando o CLI do Azure

O backup de banco de dados do Azure para servidores MySQL é feito periodicamente para habilitar os recursos de restauração. Usando esse recurso, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior no tempo, em um novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [servidor de banco de dados do Azure para MySQL e um banco de dados](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Este guia de instruções requer que você use CLI do Azure versão 2,0 ou posterior. Para confirmar a versão, no prompt de comando CLI do Azure, digite `az --version`. Para instalar ou atualizar, consulte [instalar CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Definir configuração de backup

Você faz a escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor. 

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geograficamente redundante versus redundância local, não pode ser alternado.
>

Ao criar um servidor por meio do comando `az mysql server create`, o parâmetro `--geo-redundant-backup` decide sua opção de redundância de backup. Se `Enabled`, os backups com redundância geográfica serão feitos. Ou se `Disabled` backups com redundância local forem feitos. 

O período de retenção de backup é definido pelo parâmetro `--backup-retention`. 

Para obter mais informações sobre como definir esses valores durante a criação, consulte o [início rápido da CLI do servidor de banco de dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

O período de retenção de backup de um servidor pode ser alterado da seguinte maneira:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

O exemplo anterior altera o período de retenção de backup de mydemoserver para 10 dias.

O período de retenção de backup controla o quanto o tempo uma restauração pontual pode ser recuperada, pois ela é baseada em backups disponíveis. A restauração pontual é descrita mais detalhadamente na próxima seção.

## <a name="server-point-in-time-restore"></a>Restauração pontual do servidor
Você pode restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente é deixado como está. Por exemplo, se uma tabela for acidentalmente descartada ao meio-dia de hoje, você poderá restaurar para a hora logo antes do meio-dia. Em seguida, você pode recuperar a tabela e os dados ausentes da cópia restaurada do servidor. 

Para restaurar o servidor, use o comando CLI do Azure [AZ MySQL Server Restore](/cli/azure/mysql/server#az-mysql-server-restore) .

### <a name="run-the-restore-command"></a>Executar o comando Restore

Para restaurar o servidor, no prompt de comando CLI do Azure, digite o seguinte comando:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

O comando `az mysql server restore` requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos no qual o servidor de origem existe.  |
| nome | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecione um ponto no tempo para o qual restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Use o formato de data e hora do ISO8601. Por exemplo, você pode usar seu próprio fuso horário local, como `2018-03-13T05:59:00-08:00`. Você também pode usar o formato UTC Zulu, por exemplo, `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID do servidor de origem do qual pretende restaurar. |

Quando você restaura um servidor para um ponto anterior no tempo, um novo servidor é criado. O servidor original e seus bancos de dados do ponto especificado no tempo são copiados para o novo servidor.

Os valores de local e tipo de preço para o servidor restaurado permanecem os mesmos que o servidor original. 

Após a conclusão do processo de restauração, localize o novo servidor e verifique se os dados foram restaurados conforme o esperado. O novo servidor tem o mesmo nome de logon e senha do administrador do servidor que eram válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem as regras de firewall ou os pontos de extremidade de serviço de VNet que existiam no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor.

## <a name="geo-restore"></a>Restauração geográfica
Se você configurou o servidor para backups com redundância geográfica, um novo servidor pode ser criado a partir do backup desse servidor existente. Esse novo servidor pode ser criado em qualquer região em que o banco de dados do Azure para MySQL esteja disponível.  

Para criar um servidor usando um backup com redundância geográfica, use o comando CLI do Azure `az mysql server georestore`.

> [!NOTE]
> Quando um servidor é criado pela primeira vez, ele pode não estar imediatamente disponível para a restauração geográfica. Pode levar algumas horas para que os metadados necessários sejam preenchidos.
>

Para restaurar a localização geográfica do servidor, no prompt de comando CLI do Azure, digite o seguinte comando:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Este comando cria um novo servidor chamado *mydemoserver-georestore* no leste dos EUA que pertencerá ao *MyResource*. É um servidor Uso Geral, Gen 5 com 8 vCores. O servidor é criado a partir do backup com redundância geográfica de *mydemoserver*, que também está no grupo de recursos *MyResource* Group

Se você quiser criar o novo servidor em um grupo de recursos diferente do servidor existente, no parâmetro `--source-server`, você qualificaria o nome do servidor como no exemplo a seguir:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

O comando `az mysql server georestore` requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|resource-group| myResourceGroup | O nome do grupo de recursos ao qual o novo servidor pertencerá.|
|nome | mydemoserver-georestaurado | O nome do novo servidor. |
|source-server | mydemoserver | O nome do servidor existente cujos backups com redundância geográfica são usados. |
|localização | eastus | O local do novo servidor. |
|nome de SKU| GP_Gen5_8 | Esse parâmetro define o tipo de preço, a geração de computação e o número de vCores do novo servidor. GP_Gen5_8 mapeia para um servidor Uso Geral, Gen 5 com 8 vCores.|

Ao criar um novo servidor por uma restauração geográfica, ele herda o mesmo tamanho de armazenamento e tipo de preço que o servidor de origem. Esses valores não podem ser alterados durante a criação. Depois que o novo servidor é criado, seu tamanho de armazenamento pode ser escalado verticalmente.

Após a conclusão do processo de restauração, localize o novo servidor e verifique se os dados foram restaurados conforme o esperado. O novo servidor tem o mesmo nome de logon e senha do administrador do servidor que eram válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem as regras de firewall ou os pontos de extremidade de serviço de VNet que existiam no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [backups](concepts-backup.md) do serviço
- Saiba mais sobre [réplicas](concepts-read-replicas.md)
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md)
