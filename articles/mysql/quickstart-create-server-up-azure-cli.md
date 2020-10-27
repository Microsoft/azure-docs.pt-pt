---
title: 'Quickstart: Criar base de dados Azure para o MySQL usando az mysql up'
description: Guia quickstart para criar base de dados Azure para servidor MySQL usando o comando Azure CLI (interface de linha de comando).
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a02d2e32a035581d486f22b97fc789957d0b4138
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534044"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Quickstart: Criar uma base de dados Azure para o MySQL utilizando um simples comando Azure CLI - az mysql up (pré-visualização)

> [!IMPORTANT]
> O comando [Az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI está em pré-visualização.

A Base de Dados do Azure para MySQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. O CLI Azure é utilizado para criar e gerir recursos Azure a partir da linha de comando ou em scripts. Este quickstart mostra-lhe como usar o comando [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) para criar uma Base de Dados Azure para o servidor MySQL utilizando o CLI Azure. Além de criar o servidor, o `az mysql up` comando cria uma base de dados de amostras, um utilizador de raiz na base de dados, abre a firewall para os serviços Azure e cria regras de firewall padrão para o computador cliente. Isto ajuda a acelerar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/authenticate-azure-cli) Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade de **ID** por subscrição da saída **de login az** para a sua subscrição no espaço reservado de iD de subscrição.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Para utilizar os comandos, instale a extensão [db-up.](/cli/azure/ext/db-up) Se for devolvido um erro, certifique-se de que instalou a versão mais recente do Azure CLI. Consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Criar uma base de dados Azure para o servidor MySQL utilizando o seguinte comando:

```azurecli
az mysql up
```

O servidor é criado com os seguintes valores predefinidos (a menos que os substitua manualmente):

**Definição** | **Valor predefinido** | **Descrição**
---|---|---
server-name | Sistema gerado | Um nome exclusivo que identifica a sua Base de Dados do Azure para o servidor MySQL.
resource-group | Sistema gerado | Um novo grupo de recursos Azure.
sku-name | GP_Gen5_2 | O nome do SKU. Segue a convenção {escalão de preço}\_{geração de computação}\_{vCores} em estenografia. O padrão é um servidor Gen5 de propósito geral com 2 vCores. Consulte [a](https://azure.microsoft.com/pricing/details/mysql/) nossa página de preços para mais informações sobre os níveis.
backup-retention | 7 | Quando tempo se deve reter uma cópia de segurança. A unidade é dias.
geo-redundant-backup | Desativado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não.
localização | westus2 | A localização do Azure para o servidor.
ssl-enforcement | Ativado | Se o SSL deve ser ativado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
versão | 5.7 | A versão principal do MySQL.
admin-user | Sistema gerado | O nome de utilizador para o início de sessão do administrador.
admin-password | Sistema gerado | A palavra-passe do utilizador administrador.

> [!NOTE]
> Para obter mais informações sobre o `az mysql up` comando e os seus parâmetros adicionais, consulte a documentação do [Azure CLI](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Uma vez criado o seu servidor, ele vem com as seguintes definições:

- É criada uma regra de firewall chamada "devbox". O CLI Azure tenta detetar o endereço IP da máquina de onde o `az mysql up` comando é executado e permite esse endereço IP.
- "Permitir o acesso aos serviços Azure" está definido para ON. Esta definição configura a firewall do servidor para aceitar ligações de todos os recursos Azure, incluindo recursos não na sua subscrição.
- O `wait_timeout` parâmetro está definido para 8 horas
- Uma base de dados vazia chamada "sampledb" é criada
- Um novo utilizador chamado "raiz" com privilégios para "sampledb" é criado

> [!NOTE]
> A Azure Database for MySQL comunica sobre a porta 3306. Ao ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 3306 pode não ser permitido pela firewall da sua rede. Tenha o seu departamento de TI aberto porta 3306 para ligar ao seu servidor.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Após a conclusão do `az mysql up` comando, é devolvida uma lista de cadeias de ligação para linguagens de programação populares. Estas cadeias de conexão estão pré-configuradas com os atributos específicos da sua recém-criada Base de Dados Azure para o servidor MySQL.

Pode utilizar o comando [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) para listar estas cordas de ligação novamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe todos os recursos que criou no arranque rápido utilizando o seguinte comando. Este comando elimina a Base de Dados Azure para o servidor MySQL e para o grupo de recursos.

```azurecli
az mysql down --delete-group
```

Se quiser eliminar o servidor recém-criado, pode executar o comando [az mysql down.](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down)

```azurecli
az mysql down
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design a MySQL Database with Azure CLI](./tutorial-design-database-using-cli.md) (Conceber uma Base de Dados MySQL com a CLI do Azure)
