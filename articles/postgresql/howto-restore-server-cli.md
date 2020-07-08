---
title: Backup e restauro - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como configurar configurações de backup e restaurar um servidor na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o CLI Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f0ea24133d7b6acdc4b099ee21a8711a2d99095d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74775709"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Como fazer o back up e restaurar um servidor na Base de Dados Azure para PostgreSQL - Servidor Único usando o CLI Azure

A base de dados Azure para servidores PostgreSQL é monitorizada periodicamente para permitir funcionalidades de Restauro. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num ponto-a-tempo anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor e base de dados PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no pedido de comando do Azure CLI, insira `az --version` . Para instalar ou atualizar, consulte [instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Configuração de backup de definição

Faz a escolha entre configurar o seu servidor para cópias de segurança locais redundantes ou backups geograficamente redundantes na criação do servidor. 

> [!NOTE]
> Depois de um servidor ser criado, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser trocado.
>

Ao criar um servidor através do `az postgres server create` comando, o `--geo-redundant-backup` parâmetro decide a sua Opção de Redundância de Backup. Se `Enabled` forem tomadas cópias de segurança geo redundantes. Ou se `Disabled` os reforços locais redundantes forem levados. 

O período de retenção de backup é definido pelo parâmetro `--backup-retention-days` . 

Para obter mais informações sobre a definição destes valores durante a criação, consulte a [Base de Dados Azure para o servidor CLI Quickstart do servidor PostgreSQL](quickstart-create-server-database-azure-cli.md).

O período de retenção de backup de um servidor pode ser alterado da seguinte forma:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

O exemplo anterior altera o período de retenção de backup do mydemoserver para 10 dias.

O período de retenção de backups regula o quão longe no tempo um restauro de ponto no tempo pode ser recuperado, uma vez que é baseado em backups disponíveis. A restauração pontual é descrita mais na secção seguinte.

## <a name="server-point-in-time-restore"></a>Restauro ponto-a-tempo do servidor
Pode restaurar o servidor num ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor, e o servidor existente é deixado como está. Por exemplo, se uma mesa for acidentalmente largada ao meio-dia de hoje, pode restaurar a hora pouco antes do meio-dia. Em seguida, pode recuperar a tabela e os dados em falta a partir da cópia restaurada do servidor. 

Para restaurar o servidor, utilize o comando de restauro do servidor Azure CLI [az postgres.](/cli/azure/postgres/server)

### <a name="run-the-restore-command"></a>Executar o comando de restauro

Para restaurar o servidor, na introdução do comando Azure CLI, insira o seguinte comando:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

O `az postgres server restore` comando requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos onde existe o servidor de origem.  |
| name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecione um ponto no tempo para restaurar. Esta data e hora têm de estar dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seu próprio fuso horário local, como `2018-03-13T05:59:00-08:00` . Também pode utilizar o formato UTC Zulu, por exemplo, `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | O nome ou ID do servidor de origem do qual pretende restaurar. |

Quando restaura um servidor a um ponto anterior no tempo, é criado um novo servidor. O servidor original e as suas bases de dados a partir do ponto especificado no tempo são copiados para o novo servidor.

Os valores de localização e de nível de preços do servidor restaurado permanecem os mesmos que o servidor original. 

Após o fim do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante o restauro não tem as regras de firewall ou os pontos finais de serviço VNet que existiam no servidor original. Estas regras têm de ser configuradas separadamente para este novo servidor.

## <a name="geo-restore"></a>Restauro geo
Se configurar o servidor para cópias de segurança geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança desse servidor existente. Este novo servidor pode ser criado em qualquer região onde a Base de Dados Azure para PostgreSQL esteja disponível.  

Para criar um servidor utilizando uma cópia de segurança geo redundante, utilize o comando Azure `az postgres server georestore` CLI.

> [!NOTE]
> Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.
>

Para restaurar geo restaurar o servidor, na pronta de comando Azure CLI, insira o seguinte comando:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Este comando cria um novo servidor chamado *mydemoserver-georestored* no Leste dos EUA que pertencerá ao *myresourcegroup*. É um servidor De Propósito Geral, Gen 4 com 8 vCores. O servidor é criado a partir da cópia de segurança geo-redundante do *mydemoserver,* que também está no grupo de recursos *myresourcegroup*

Se pretender criar o novo servidor num grupo de recursos diferente do servidor existente, então no `--source-server` parâmetro qualificaria o nome do servidor como no seguinte exemplo:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

O `az postgres server georestore` comando requer os seguintes parâmetros:

| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
|resource-group| myResourceGroup | O nome do grupo de recursos a que o novo servidor pertencerá.|
|name | mydemoserver-georestored | O nome do novo servidor. |
|source-server | mydemoserver | O nome do servidor existente cujas cópias de segurança geo redundantes são utilizadas. |
|localização | eastus | A localização do novo servidor. |
|sku-name| GP_Gen4_8 | Este parâmetro define o nível de preços, geração de cálculo e número de vCores do novo servidor. GP_Gen4_8 mapas para um servidor de Propósito Geral, Gen 4 com 8 vCores.|

Ao criar um novo servidor por um restauro geo, herda o mesmo tamanho de armazenamento e nível de preços que o servidor de origem. Estes valores não podem ser alterados durante a criação. Após a criação do novo servidor, o seu tamanho de armazenamento pode ser aumentado.

Após o fim do processo de restauro, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante o restauro não tem as regras de firewall ou os pontos finais de serviço VNet que existiam no servidor original. Estas regras têm de ser configuradas separadamente para este novo servidor.

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre as [cópias](concepts-backup.md) de segurança do serviço
- Saiba mais [sobre réplicas](concepts-read-replicas.md)
- Saiba mais sobre opções [de continuidade de negócios](concepts-business-continuity.md)
