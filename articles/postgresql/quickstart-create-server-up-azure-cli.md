---
title: 'Quickstart: Create server - az postgres up - Base de Dados Azure para PostgreSQL - Servidor Único'
description: Guia de arranque rápido para criar base de dados Azure para PostgreSQL - Servidor Único utilizando o comando Azure CLI (interface de linha de comando).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74774837"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Quickstart: Use um comando Azure CLI, az postgres up (pré-visualização), para criar uma Base de Dados Azure para PostgreSQL - Servidor Único

> [!IMPORTANT]
> O comando [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI está em pré-visualização.

A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o comando [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) para criar uma Base de Dados Azure para servidor PostgreSQL utilizando o Azure CLI. Além de criar o `az postgres up` servidor, o comando cria uma base de dados de amostras, um utilizador raiz na base de dados, abre a firewall para os serviços Azure e cria regras de firewall padrão para o computador cliente. Estes incumprimentos ajudam a acelerar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de fazer login na sua conta usando o comando [de login az.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Note a propriedade **ID** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade de ID de **subscrição** da saída **de login az** para a sua subscrição no espaço reservado de ID de subscrição.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Para utilizar os comandos, instale a extensão [db-up.](/cli/azure/ext/db-up) Se um erro for devolvido, certifique-se de que instalou a versão mais recente do Azure CLI. Ver [Instalar o Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Criar uma Base de Dados Azure para servidor PostgreSQL utilizando o seguinte comando:

```azurecli
az postgres up
```

O servidor é criado com os seguintes valores predefinidos (a menos que os sobrepor manualmente):

**Definição** | **Default value** (Valor predefinido) | **Descrição**
---|---|---
server-name | Sistema gerado | Um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL.
resource-group | Sistema gerado | Um novo grupo de recursos Azure.
sku-name | GP_Gen5_2 | O nome do SKU. Segue a convenção {escalão de preço}\_{geração de computação}\_{vCores} em estenografia. O padrão é um servidor General Purpose Gen5 com 2 vCores. Consulte [a](https://azure.microsoft.com/pricing/details/postgresql/) nossa página de preços para mais informações sobre os níveis.
backup-retention | 7 | Quanto tempo uma reserva é retida. A unidade é dias.
geo-redundant-backup | Desativado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não.
localização | westus2 | A localização do Azure para o servidor.
ssl-enforcement | Desativado | Se SSL deve ser ativado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
versão | 10 | A versão principal do PostgreSQL.
admin-user | Sistema gerado | O nome de utilizador do administrador.
admin-password | Sistema gerado | A palavra-passe do utilizador administrador.

> [!NOTE]
> Para obter mais `az postgres up` informações sobre o comando e os seus parâmetros adicionais, consulte a [documentação Azure CLI](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Uma vez criado o servidor, vem com as seguintes definições:

- É criada uma regra de firewall chamada "devbox". O Azure CLI tenta detetar o endereço `az postgres up` IP da máquina de onde o comando é executado e listas de brancos desse endereço IP.
- "Permitir o acesso aos serviços Azure" está definido para ON. Esta definição configura a firewall do servidor para aceitar ligações de todos os recursos do Azure, incluindo recursos não na sua subscrição.
- É criada uma base de dados vazia chamada "sampledb"
- É criado um novo utilizador chamado "raiz" com privilégios para "sampledb"

> [!NOTE]
> A Base de Dados Azure para PostgreSQL comunica sobre a porta 5432. Ao ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Tenha o seu departamento de TI em porta aberta 5432 para ligar ao seu servidor.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Após `az postgres up` o comando ser concluído, uma lista de cordas de ligação para linguagens de programação populares é devolvida a você. Estas cordas de ligação são pré-configuradas com os atributos específicos da sua recém-criada Base de Dados Azure para o servidor PostgreSQL.

Pode utilizar o comando de cordas de [conexão az postgres](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) para listar estas cordas de ligação novamente.

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que criou no arranque rápido usando o seguinte comando. Este comando elimina a Base de Dados Azure para o servidor PostgreSQL e o grupo de recursos.

```azurecli
az postgres down --delete-group
```

Se quiser apenas eliminar o servidor recém-criado, pode executar o comando [az postgres down.](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down)

```azurecli
az postgres down
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
