---
title: 'Início rápido: criar servidor-AZ postgres up-banco de dados do Azure para PostgreSQL-servidor único'
description: Guia de início rápido para criar um banco de dados do Azure para PostgreSQL-servidor único usando o comando CLI do Azure (interface de linha de comando) up.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774837"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Início rápido: usar um comando CLI do Azure, AZ postgres up (versão prévia), para criar um banco de dados do Azure para PostgreSQL-servidor único

> [!IMPORTANT]
> O comando [AZ postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) CLI do Azure está em versão prévia.

A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de início rápido mostra como usar o comando [AZ postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) para criar um banco de dados do Azure para o servidor PostgreSQL usando o CLI do Azure. Além de criar o servidor, o comando `az postgres up` cria um banco de dados de exemplo, um usuário raiz no banco de dados, abre o firewall para os serviços do Azure e cria regras de firewall padrão para o computador cliente. Esses padrões ajudam a agilizar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que você esteja executando o CLI do Azure versão 2,0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará entrar em sua conta usando o comando [AZ login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) . Observe a propriedade **ID** da saída do comando para o nome da assinatura correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade **ID da assinatura** da saída de **logon AZ** para sua assinatura no espaço reservado ID da assinatura.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Para usar os comandos, instale a extensão [DB-up](/cli/azure/ext/db-up) . Se um erro for retornado, verifique se você instalou a versão mais recente do CLI do Azure. Consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Crie um servidor de banco de dados do Azure para PostgreSQL usando o seguinte comando:

```azurecli
az postgres up
```

O servidor é criado com os seguintes valores padrão (a menos que você os substitua manualmente):

**Definição** | **Default value** (Valor predefinido) | **Descrição**
---|---|---
server-name | Gerado pelo sistema | Um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL.
resource-group | Gerado pelo sistema | Um novo grupo de recursos do Azure.
nome de SKU | GP_Gen5_2 | O nome de SKU. Segue a convenção {escalão de preço}\_{geração de computação}\_{vCores} em estenografia. O padrão é um servidor Uso Geral Gen5 com 2 vCores. Consulte nossa [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para obter mais informações sobre as camadas.
backup-retention | 7 | Por quanto tempo um backup é retido. A unidade é dias.
geo-redundant-backup | Desativado | Se as cópias de segurança georredundantes devem estar ativadas para este servidor ou não.
localização | westus2 | A localização do Azure para o servidor.
ssl-enforcement | Desativado | Se SSL deve ser ativado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
versão | 10 | A versão principal do PostgreSQL.
admin-user | Gerado pelo sistema | O nome de usuário para o administrador.
admin-password | Gerado pelo sistema | A palavra-passe do utilizador administrador.

> [!NOTE]
> Para obter mais informações sobre o comando `az postgres up` e seus parâmetros adicionais, consulte a [documentação do CLI do Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Depois que o servidor é criado, ele vem com as seguintes configurações:

- Uma regra de firewall chamada "devbox" é criada. O CLI do Azure tenta detectar o endereço IP do computador no qual o comando `az postgres up` é executado e lista de permissões esse endereço IP.
- "Permitir acesso aos serviços do Azure" está definido como ativado. Essa configuração configura o Firewall do servidor para aceitar conexões de todos os recursos do Azure, incluindo recursos que não estão em sua assinatura.
- Um banco de dados vazio chamado "SampleDB" é criado
- Um novo usuário chamado "root" com privilégios para "SampleDB" é criado

> [!NOTE]
> O banco de dados do Azure para PostgreSQL se comunica pela porta 5432. Ao ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Peça que seu departamento de ti Abra a porta 5432 para se conectar ao servidor.

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Depois que o comando `az postgres up` for concluído, uma lista de cadeias de conexão para linguagens de programação populares será retornada para você. Essas cadeias de conexão são pré-configuradas com os atributos específicos do seu banco de dados do Azure recém-criado para o servidor PostgreSQL.

Você pode usar o comando [AZ postgres show-Connection-String](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) para listar essas cadeias de conexão novamente.

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que você criou no início rápido usando o comando a seguir. Este comando exclui o banco de dados do Azure para o servidor PostgreSQL e o grupo de recursos.

```azurecli
az postgres down --delete-group
```

Se você quiser apenas excluir o servidor recém-criado, poderá executar o comando [AZ postgres down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) .

```azurecli
az postgres down
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
