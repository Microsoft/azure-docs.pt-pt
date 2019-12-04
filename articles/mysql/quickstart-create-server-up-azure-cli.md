---
title: 'Início rápido: criar banco de dados do Azure para MySQL usando AZ MySQL up'
description: Guia de início rápido para criar o banco de dados do Azure para servidor MySQL usando o comando CLI do Azure (interface de linha de comando) up.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: 4bb5c62a7df53548ff59a03c6ccc8fb28f1503d3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765688"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Início rápido: criar um banco de dados do Azure para MySQL usando um comando CLI do Azure simples – AZ MySQL up (versão prévia)

> [!IMPORTANT]
> O comando [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) CLI do Azure está em versão prévia.

A Base de Dados do Azure para MySQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de início rápido mostra como usar o comando [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) para criar um servidor de banco de dados do Azure para MySQL usando o CLI do Azure. Além de criar o servidor, o comando `az mysql up` cria um banco de dados de exemplo, um usuário raiz no banco de dados, abre o firewall para os serviços do Azure e cria regras de firewall padrão para o computador cliente. Isso ajuda a agilizar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que você esteja executando o CLI do Azure versão 2,0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [AZ login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) . Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade **ID da assinatura** da saída de **logon AZ** para sua assinatura no espaço reservado ID da assinatura.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Para usar os comandos, instale a extensão [DB-up](/cli/azure/ext/db-up) . Se um erro for retornado, verifique se você instalou a versão mais recente do CLI do Azure. Consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Crie um servidor de banco de dados do Azure para MySQL usando o seguinte comando:

```azurecli
az mysql up
```

O servidor é criado com os seguintes valores padrão (a menos que você os substitua manualmente):

**Definição** | **Default value** (Valor predefinido) | **Descrição**
---|---|---
server-name | Gerado pelo sistema | Um nome exclusivo que identifica a sua Base de Dados do Azure para o servidor MySQL.
resource-group | Gerado pelo sistema | Um novo grupo de recursos do Azure.
nome de SKU | GP_Gen5_2 | O nome de SKU. Segue a convenção {escalão de preço}\_{geração de computação}\_{vCores} em estenografia. O padrão é um servidor Uso Geral Gen5 com 2 vCores. Consulte nossa [página de preços](https://azure.microsoft.com/pricing/details/mysql/) para obter mais informações sobre as camadas.
backup-retention | 7 | Quando tempo se deve reter uma cópia de segurança. A unidade é dias.
geo-redundant-backup | Desativado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não.
localização | westus2 | A localização do Azure para o servidor.
ssl-enforcement | Desativado | Se SSL deve ser ativado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
versão | 5.7 | A versão principal do MySQL.
admin-user | Gerado pelo sistema | O nome de utilizador para o início de sessão do administrador.
admin-password | Gerado pelo sistema | A palavra-passe do utilizador administrador.

> [!NOTE]
> Para obter mais informações sobre o comando `az mysql up` e seus parâmetros adicionais, consulte a [documentação do CLI do Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Depois que o servidor é criado, ele vem com as seguintes configurações:

- Uma regra de firewall chamada "devbox" é criada. O CLI do Azure tenta detectar o endereço IP do computador no qual o comando `az mysql up` é executado e lista de permissões esse endereço IP.
- "Permitir acesso aos serviços do Azure" está definido como ativado. Essa configuração configura o Firewall do servidor para aceitar conexões de todos os recursos do Azure, incluindo recursos que não estão em sua assinatura.
- O parâmetro `wait_timeout` é definido como 8 horas
- Um banco de dados vazio chamado "SampleDB" é criado
- Um novo usuário chamado "root" com privilégios para "SampleDB" é criado

> [!NOTE]
> O banco de dados do Azure para MySQL se comunica pela porta 3306. Ao conectar-se de dentro de uma rede corporativa, o tráfego de saída pela porta 3306 pode não ser permitido pelo firewall de sua rede. Peça que seu departamento de ti Abra a porta 3306 para se conectar ao servidor.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Depois que o comando `az mysql up` for concluído, uma lista de cadeias de conexão para linguagens de programação populares será retornada para você. Essas cadeias de conexão são pré-configuradas com os atributos específicos do seu banco de dados do Azure recém-criado para o servidor MySQL.

Você pode usar o comando [AZ MySQL show-Connection-String](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) para listar essas cadeias de conexão novamente.

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que você criou no início rápido usando o comando a seguir. Este comando exclui o servidor de banco de dados do Azure para MySQL e o grupo de recursos.

```azurecli
az mysql down --delete-group
```

Se você quiser apenas excluir o servidor recém-criado, poderá executar o comando [AZ MySQL down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) .

```azurecli
az mysql down
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design a MySQL Database with Azure CLI](./tutorial-design-database-using-cli.md) (Conceber uma Base de Dados MySQL com a CLI do Azure)
