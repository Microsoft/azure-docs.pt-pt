---
title: Gerir servidor - Azure CLI - Azure Database for MySQL
description: Saiba como gerir uma Base de Dados Azure para o servidor MySQL a partir do Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: bd068f0cf76a8edefca854d72d5240c0becaf8fc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542070"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Gerir uma base de dados Azure para o mySQL Single servidor utilizando o Azure CLI

Este artigo mostra-lhe como gerir os seus servidores Single implantados no Azure. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

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

Se ainda não criou um corte, consulte este [quickstart](quickstart-create-mysql-server-database-using-azure-cli.md) para criar um.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala
Pode aumentar o seu nível de preços, calcular e armazenar facilmente utilizando o seguinte comando. Pode ver toda a operação do servidor que pode realizar [a visão geral do servidor az mysql](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Aqui estão os detalhes dos argumentos acima:

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Introduza um nome único para a sua Base de Dados Azure para o servidor MySQL. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
sku-name|GP_Gen5_2|Insira o nome do nível de preços e configuração de cálculo. Segue a convenção {escalão de preço} _{geração de computação}_ {vCores} em estenografia. Consulte os [níveis de preços](./concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | A capacidade de armazenamento do servidor (a unidade é megabytes). Mínimo 5120 e aumentos em 1024 incrementos.

> [!Important]
> - O armazenamento pode ser dimensionado (no entanto, não é possível reduzir o armazenamento)
> - O escalonamento de Básico para Propósito Geral ou nível de preços otimizados de memória não é suportado. Você pode escalar manualmente com  [um script de bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) ou [usando mySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-mysql-databases-on-a-server"></a>Gerir bases de dados mySQL num servidor
Pode utilizar qualquer um destes comandos para criar, eliminar, listar e visualizar propriedades de base de dados de uma base de dados no seu servidor

| Cmdlet | Utilização| Description |
| --- | ---| --- |
|[az mysql db criar](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Cria uma base de dados|
|[az mysql db apagar](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Elimine a sua base de dados do seu servidor. Este comando não apaga o seu servidor. |
|[lista az mysql db](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|lista todas as bases de dados no servidor|
|[az mysql db show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra mais detalhes da base de dados|

## <a name="update-admin-password"></a>Atualizar senha de administração
Pode alterar a palavra-passe da função do administrador com este comando
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Certifique-se de que a palavra-passe é mínima de 8 caracteres e máximo de 128 caracteres.
> A palavra-passe deve conter caracteres de três das seguintes categorias: letras maiúsculas em inglês, letras minúsculas, números e caracteres não alfanuméricos.

## <a name="delete-a-server"></a>Excluir um servidor
Se quiser eliminar o servidor MySQL Single, pode executar [o comando de eliminação do servidor Az Mysql.](/cli/azure/mysql/server#az-mysql-server-delete)

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- [Reiniciar um servidor](howto-restart-server-cli.md)
- [Restaurar um servidor em mau estado](howto-restore-server-cli.md)
- [Monitorizar e otimizar o servidor](concepts-monitoring.md)