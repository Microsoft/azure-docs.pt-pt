---
title: 'Quickstart: Crie base de dados Azure para MySQL usando az mysql up'
description: Guia de arranque rápido para criar base de dados Azure para servidor MySQL utilizando o comando Azure CLI (interface de linha de comando).
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067709"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Quickstart: Criar uma Base de Dados Azure para o MySQL utilizando um simples comando Azure CLI - az mysql up (pré-visualização)

> [!IMPORTANT]
> O comando [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI está em pré-visualização.

A Base de Dados do Azure para MySQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. O Azure CLI é utilizado para criar e gerir recursos Azure a partir da linha de comando ou em scripts. Este quickstart mostra-lhe como usar o comando [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) para criar uma Base de Dados Azure para o servidor MySQL usando o Azure CLI. Além de criar o `az mysql up` servidor, o comando cria uma base de dados de amostras, um utilizador raiz na base de dados, abre a firewall para os serviços Azure e cria regras de firewall padrão para o computador cliente. Isto ajuda a acelerar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de fazer login na sua conta utilizando o comando [de login az.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade de ID de **subscrição** da saída **de login az** para a sua subscrição no espaço reservado de ID de subscrição.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Para utilizar os comandos, instale a extensão [db-up.](/cli/azure/ext/db-up) Se um erro for devolvido, certifique-se de que instalou a versão mais recente do Azure CLI. Ver [Instalar o Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Criar uma Base de Dados Azure para servidor MySQL utilizando o seguinte comando:

```azurecli
az mysql up
```

O servidor é criado com os seguintes valores predefinidos (a menos que os sobrepor manualmente):

**Definição** | **Default value** (Valor predefinido) | **Descrição**
---|---|---
server-name | Sistema gerado | Um nome exclusivo que identifica a sua Base de Dados do Azure para o servidor MySQL.
resource-group | Sistema gerado | Um novo grupo de recursos Azure.
sku-name | GP_Gen5_2 | O nome do SKU. Segue a convenção {escalão de preço}\_{geração de computação}\_{vCores} em estenografia. O padrão é um servidor General Purpose Gen5 com 2 vCores. Consulte [a](https://azure.microsoft.com/pricing/details/mysql/) nossa página de preços para mais informações sobre os níveis.
backup-retention | 7 | Quando tempo se deve reter uma cópia de segurança. A unidade é dias.
geo-redundant-backup | Desativado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não.
localização | westus2 | A localização do Azure para o servidor.
ssl-enforcement | Desativado | Se o SSL deve ser ativado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
versão | 5.7 | A versão principal do MySQL.
admin-user | Sistema gerado | O nome de utilizador para o início de sessão do administrador.
admin-password | Sistema gerado | A palavra-passe do utilizador administrador.

> [!NOTE]
> Para obter mais `az mysql up` informações sobre o comando e os seus parâmetros adicionais, consulte a [documentação Azure CLI](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Uma vez criado o servidor, vem com as seguintes definições:

- É criada uma regra de firewall chamada "devbox". O Azure CLI tenta detetar o endereço `az mysql up` IP da máquina de onde o comando é executado e listas de brancos desse endereço IP.
- "Permitir o acesso aos serviços Azure" está definido para ON. Esta definição configura a firewall do servidor para aceitar ligações de todos os recursos do Azure, incluindo recursos não na sua subscrição.
- O `wait_timeout` parâmetro está definido para 8 horas
- É criada uma base de dados vazia chamada "sampledb"
- É criado um novo utilizador chamado "raiz" com privilégios para "sampledb"

> [!NOTE]
> A Base de Dados Azure para MySQL comunica sobre a porta 3306. Ao ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 3306 não pode ser permitido pela firewall da sua rede. Tenha o seu departamento de TI em porta aberta 3306 para ligar ao seu servidor.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Após `az mysql up` o comando ser concluído, uma lista de cordas de ligação para linguagens de programação populares é devolvida a você. Estas cordas de ligação são pré-configuradas com os atributos específicos da sua recém-criada Base de Dados Azure para o servidor MySQL.

Pode usar o comando de cordas de [conexão az mysql](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) para listar estas cordas de ligação novamente.

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que criou no arranque rápido usando o seguinte comando. Este comando elimina a Base de Dados Azure para o servidor MySQL e o grupo de recursos.

```azurecli
az mysql down --delete-group
```

Se quiser apenas apagar o servidor recém-criado, pode executar o comando [az mysql](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) down.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design a MySQL Database with Azure CLI](./tutorial-design-database-using-cli.md) (Conceber uma Base de Dados MySQL com a CLI do Azure)
