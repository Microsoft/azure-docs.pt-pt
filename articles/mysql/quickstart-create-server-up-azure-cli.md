---
title: Início rápido - criar uma base de dados do Azure para MySQL através de um comando da CLI do Azure simples - az mysql cópia de segurança (pré-visualização)
description: Guia de início rápido para criar a base de dados do Azure para o servidor MySQL com a CLI do Azure (interface de linha de comandos) comando.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.custom: mvc
ms.openlocfilehash: aa0d2a9e990faa8d99355744824f34e26aeb519e
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137917"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Início rápido: Criar uma base de dados do Azure para MySQL através de um comando da CLI do Azure simples - az mysql cópia de segurança (pré-visualização)

> [!IMPORTANT]
> O [az mysql se](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) comando da CLI do Azure está em pré-visualização.

A Base de Dados do Azure para MySQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de introdução mostra-lhe como utilizar o [az mysql se](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) comando para criar uma base de dados do Azure para o servidor MySQL com a CLI do Azure. Além de criar o servidor, o `az mysql up` comando cria uma base de dados de exemplo, um utilizador de raiz na base de dados, abre-se a firewall para serviços do Azure e cria predefinidas regras de firewall para o computador cliente. Isto ajuda a acelerar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer a que está a executar a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta com o [início de sessão az](/cli/azure/authenticate-azure-cli?view=interactive-log-in) comando. Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a **id de subscrição** propriedade a partir do **início de sessão az** de saída para a sua subscrição para o marcador de posição de id de subscrição.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Para utilizar os comandos, instale o [db-up](/cli/azure/ext/db-up) extensão. Se for devolvido um erro, certifique-se de que instalou a versão mais recente da CLI do Azure. Ver [instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Crie uma base de dados do Azure para o servidor MySQL com o seguinte comando:

```azurecli
az mysql up
```

O servidor é criado com os seguintes valores predefinidos (a menos que as substitua manualmente):

**Definição** | **Default value** (Valor predefinido) | **Descrição**
---|---|---
server-name | Gerada pelo sistema | Um nome exclusivo que identifica a sua Base de Dados do Azure para o servidor MySQL.
resource-group | Gerada pelo sistema | Um novo grupo de recursos do Azure.
sku-name | GP_Gen5_2 | O nome de SKU. Segue a convenção {escalão de preço}\_{geração de computação}\_{vCores} em estenografia. A predefinição é um servidor de Gen5 de finalidade geral com 2 vCores. Consulte nossos [página de preços](https://azure.microsoft.com/pricing/details/mysql/) para obter mais informações sobre os escalões.
backup-retention | 7 | Quando tempo se deve reter uma cópia de segurança. A unidade é dias.
geo-redundant-backup | Desativado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não.
localização | westus2 | A localização do Azure para o servidor.
ssl-enforcement | Desativado | Se SSL deve ser ativado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
versão | 5.7 | A versão principal do MySQL.
admin-user | Gerada pelo sistema | O nome de utilizador para o início de sessão do administrador.
admin-password | Gerada pelo sistema | A palavra-passe do utilizador administrador.

> [!NOTE]
> Para obter mais informações sobre o `az mysql up` comando e os respetivos parâmetros adicionais, consulte a [documentação da CLI do Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Uma vez criado o seu servidor, trata-se com as seguintes definições:

- É criada uma regra de firewall chamada "devbox". A CLI do Azure tenta detetar o endereço IP da máquina a `az mysql up` comando está a ser executada a partir e de permissões esse endereço IP.
- "Permitir acesso aos serviços do Azure" está definido como ligado. Esta definição configura a firewall do servidor para aceitar ligações a partir de todos os recursos do Azure, incluindo recursos não na sua subscrição.
- O `wait_timeout` parâmetro estiver definido para 8 horas
- Criar uma base de dados vazia com o nome "sampledb"
- É criado um novo utilizador com o nome "raiz" com privilégios para "sampledb"

> [!NOTE]
> Base de dados do Azure para MySQL comunica através da porta 3306. Ao ligar-se de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido pela firewall da rede. Ter o seu departamento de TI abra a porta 3306 para se ligar ao seu servidor.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Depois do `az mysql up` comando é concluído, é devolvida uma lista de cadeias de ligação para linguagens de programação populares para. Essas cadeias de caracteres de conexão estão pré-configuradas com os atributos específicos de sua base de dados do Azure recentemente criado para o servidor MySQL.

Pode utilizar o [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) command para listar essas cadeias de caracteres de conexão novamente.

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que criou no guia de introdução com o seguinte comando. Este comando elimina a base de dados do Azure para o servidor MySQL e o grupo de recursos.

```azurecli
az mysql down --delete-group
```

Se apenas quiser eliminar o servidor recentemente criado, pode executar [az mysql para baixo](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) comando.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Design a MySQL Database with Azure CLI](./tutorial-design-database-using-cli.md) (Conceber uma Base de Dados MySQL com a CLI do Azure)
