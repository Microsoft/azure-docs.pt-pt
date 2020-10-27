---
title: Gerir o servidor - Azure CLI - Base de Dados Azure para o MySQL Flexible Server
description: Saiba como gerir uma Base de Dados Azure para o servidor flexível MySQL a partir do Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545060"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Gerir uma Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização) utilizando o Azure CLI

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo mostra-lhe como gerir o seu Servidor Flexível (Pré-visualização) implantado no Azure. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

```azurecli-interactive
az login
```

Selecione a subscrição específica sob a sua conta usando o comando [conjunto de conta az.](/cli/azure/account) Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento **de subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se ainda não criou um servidor flexível, por favor crie um para começar com este como orientar.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Pode escalar o seu nível de cálculo, vCores e armazenamento facilmente usando o seguinte comando. Pode ver toda a operação do servidor que pode realizar [a az mysql a az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Aqui estão os detalhes dos argumentos acima:

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Introduza um nome único para a sua Base de Dados Azure para o servidor MySQL. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
sku-name|Standard_D4ds_v4|Insira o nome do nível e tamanho do cálculo. Segue a convenção Standard_{VM tamanho} em abreviatura. Consulte os [níveis de preços](../concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | A capacidade de armazenamento do servidor (a unidade é megabytes). Mínimo 5120 e aumentos em 1024 incrementos.

> [!Important]
> - O armazenamento pode ser dimensionado (no entanto, não é possível reduzir o armazenamento)


## <a name="manage-mysql-databases-on-a-server"></a>Gerencie as bases de dados do MySQL num servidor.
Pode utilizar qualquer um destes comandos para criar, eliminar, listar e visualizar propriedades de base de dados de uma base de dados no seu servidor

| Cmdlet | Utilização| Descrição |
| --- | ---| --- |
|[az mysql flexible-servidor db criar](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Cria uma base de dados|
|[az mysql flexível-servidor db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Elimine a sua base de dados do seu servidor. Este comando não apaga o seu servidor. |
|[az mysql flexível-servidor db lista](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|lista todas as bases de dados no servidor|
|[az mysql flexível-servidor db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra mais detalhes da base de dados|

## <a name="update-admin-password"></a>Atualizar senha de administração
Pode alterar a palavra-passe da função do administrador com este comando
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Certifique-se de que a palavra-passe é mínima de 8 caracteres e máximo de 128 caracteres.
> A palavra-passe deve conter caracteres de três das seguintes categorias: letras maiúsculas em inglês, letras minúsculas, números e caracteres não alfanuméricos.

## <a name="delete-a-server"></a>Excluir um servidor
Se quiser eliminar o servidor Flexível MySQL, pode executar [o comando de eliminação do servidor flexível az mysql.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- [Saiba como iniciar ou parar um servidor](how-to-stop-start-server-portal.md)
- [Saiba como gerir uma rede virtual](how-to-manage-virtual-network-cli.md)
- [Resolver problemas de ligação](how-to-troubleshoot-common-connection-issues.md)
- [Criar e gerir firewall](how-to-manage-firewall-cli.md)