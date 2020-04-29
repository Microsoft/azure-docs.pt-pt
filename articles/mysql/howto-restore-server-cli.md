---
title: Backup e restauro - Azure CLI - Base de Dados Azure para MySQL
description: Aprenda a fazer backup e restaurar um servidor na Base de Dados Azure para o MySQL utilizando o Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: a2a9efceed84c4c57d1ad2cae47dd4440fd4eb42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80373002"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Como fazer cópias de segurança e restaurar um servidor na Base de Dados Azure para o MySQL utilizando o Azure CLI

A Base de Dados Azure para servidores MySQL é apoiada periodicamente para ativar as funcionalidades de Restauro. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num momento anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma base de dados Azure para servidor e base de [dados MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no aviso de `az --version`comando Azure CLI, introduza . Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Definir configuração de backup

Faça a escolha entre configurar o seu servidor para backups redundantes localmente ou cópias de segurança geograficamente redundantes na criação do servidor. 

> [!NOTE]
> Depois de criado um servidor, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser trocado.
>

Ao criar um `az mysql server create` servidor através `--geo-redundant-backup` do comando, o parâmetro decide a sua Opção de Redundância de Backup. Se, `Enabled`são tomadas cópias de segurança georedundantes. Ou `Disabled` se forem tomadas cópias de segurança redundantes localmente. 

O período de retenção de `--backup-retention`reserva é definido pelo parâmetro . 

Para obter mais informações sobre a definição destes valores durante a criação, consulte a Base de Dados Azure para o [servidor MySQL CLI Quickstart](quickstart-create-mysql-server-database-using-azure-cli.md).

O período de retenção de cópia de segurança de um servidor pode ser alterado da seguinte forma:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

O exemplo anterior altera o período de retenção de cópia de segurança do mydemoserver para 10 dias.

O período de retenção de reserva rege o quão longe no tempo um restauro pontual pode ser recuperado, uma vez que é baseado em backups disponíveis. A restauração ponto-a-tempo é descrita mais na secção seguinte.

## <a name="server-point-in-time-restore"></a>Restauro ponto-a-tempo do servidor
Pode restaurar o servidor num ponto anterior. Os dados restaurados são copiados para um novo servidor, e o servidor existente é deixado como está. Por exemplo, se uma mesa for acidentalmente largada ao meio-dia de hoje, pode restaurar a hora pouco antes do meio-dia. Em seguida, pode recuperar a tabela em falta e os dados da cópia restaurada do servidor. 

Para restaurar o servidor, utilize o comando de restauro do servidor Azure CLI [az mysql.](/cli/azure/mysql/server#az-mysql-server-restore)

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, no pedido de comando Azure CLI, introduza o seguinte comando:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

O `az mysql server restore` comando requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos onde o servidor de origem existe.  |
| nome | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecione um ponto no tempo para restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu próprio `2018-03-13T05:59:00-08:00`fuso horário local, como . Também pode utilizar o formato UTC `2018-03-13T13:59:00Z`Zulu, por exemplo, . |
| source-server | mydemoserver | O nome ou ID do servidor de origem do qual pretende restaurar. |

Quando restaura um servidor para um ponto mais cedo, é criado um novo servidor. O servidor original e as suas bases de dados a partir do ponto de tempo especificado são copiados para o novo servidor.

Os valores de nível de localização e de preços para o servidor restaurado permanecem os mesmos que o servidor original. 

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras têm de ser criadas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Geo restaurar
Se configurar o seu servidor para backups geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança desse servidor existente. Este novo servidor pode ser criado em qualquer região que o Azure Database para mySQL esteja disponível.  

Para criar um servidor utilizando uma cópia de `az mysql server georestore` segurança georedundante, utilize o comando Azure CLI.

> [!NOTE]
> Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.
>

Para restaurar o servidor, no pedido de comando Azure CLI, introduza o seguinte comando:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Este comando cria um novo servidor chamado *mydemoserver-georestored* no Leste dos EUA que pertencerá ao *myresourcegroup*. É um General Purpose, servidor Gen 5 com 8 vCores. O servidor é criado a partir da cópia de segurança georedundantdo do *mydemoserver,* que também está no grupo de recursos *myresourcegroup*

Se pretender criar o novo servidor num grupo de recursos diferente `--source-server` do servidor existente, então no parâmetro qualificaria o nome do servidor como no seguinte exemplo:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

O `az mysql server georestore` comando requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|resource-group| myResourceGroup | O nome do grupo de recursos a que o novo servidor pertencerá.|
|nome | mydemoserver-georestauro | O nome do novo servidor. |
|source-server | mydemoserver | O nome do servidor existente cujas cópias de segurança georedundantes são usadas. |
|localização | eastus | A localização do novo servidor. |
|sku-name| GP_Gen5_8 | Este parâmetro define o nível de preços, a geração de cálculo e o número de vCores do novo servidor. GP_Gen5_8 mapas para um Propósito Geral, servidor Gen 5 com 8 vCores.|

Ao criar um novo servidor por um georestauro, herda o mesmo tamanho de armazenamento e nível de preços que o servidor de origem. Estes valores não podem ser alterados durante a criação. Após a criação do novo servidor, o seu tamanho de armazenamento pode ser dimensionado.

Após o final do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras têm de ser criadas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [backups](concepts-backup.md) do serviço
- Saiba mais sobre [réplicas](concepts-read-replicas.md)
- Saiba mais sobre opções [de continuidade do negócio](concepts-business-continuity.md)
