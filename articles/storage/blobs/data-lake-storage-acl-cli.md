---
title: Use O CLI Azure para definir ACLs em Azure Data Lake Storage Gen2
description: Utilize o CLI Azure para gerir as listas de controlo de acesso (ACL) em contas de armazenamento que tenham um espaço hierárquico de nomes.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563169"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Use O CLI Azure para gerir ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como utilizar a [Interface Command-Line (CLI)](/cli/azure/) para obter, definir e atualizar as listas de controlo de acesso de diretórios e ficheiros.

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Mas também pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório parental sem ter que fazer estas alterações individualmente para cada item de criança.

[Referência](/cli/azure/storage/fs/access)  |  [Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Dar feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.14.0` ou superior.

- Uma das seguintes permissões de segurança:

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (Azure AD) que foi atribuído à função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.  

  - Utilizador próprio do contentor-alvo ou diretório ao qual planeia aplicar as definições ACL. Para definir os ACLs de forma recorrente, isto inclui todos os itens para crianças no contentor-alvo ou diretório.
  
  - Chave da conta de armazenamento.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Certifique-se de que tem a versão correta do Azure CLI instalada

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou](/cli/azure/install-azure-cli) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Verifique se a versão do Azure CLI que foi instalada é `2.14.0` ou superior utilizando o seguinte comando.

   ```azurecli
    az --version
   ```

   Se a sua versão do Azure CLI for inferior `2.14.0` a , então instale uma versão posterior. Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Ligar à conta

1. Se estiver a utilizar o Azure CLI localmente, execute o comando de login.

   ```azurecli
   az login
   ```

   Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

   Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal. Em seguida, inscreva-se com as credenciais da sua conta no navegador.

   Para saber mais sobre diferentes métodos de autenticação, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](./authorize-data-operations-cli.md).

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que irá hospedar o seu website estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

> [!NOTE]
> O exemplo apresentado neste artigo mostra a autorização do Azure Ative Directory (Azure AD). Para saber mais sobre os métodos de autorização, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](./authorize-data-operations-cli.md).

## <a name="get-acls"></a>Obter ACLs

Obtenha o ACL de um **diretório** usando o `az storage fs access show` comando.

Este exemplo obtém o ACL de um diretório e, em seguida, imprime o ACL na consola.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Obtenha as permissões de acesso de um **ficheiro** usando o `az storage fs access show` comando. 

Este exemplo obtém o ACL de um ficheiro e, em seguida, imprime o ACL à consola.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem a seguir mostra a saída depois de obter o ACL de um diretório.

![Obtenha a saída ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o próprio utilizador leu, escreveu e executou permissões. O grupo de donos só leu e executou permissões. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Definir ACLs

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a secção [ACLs update](#update-acls) deste artigo.  

Se optar por *definir* a ACL, deve adicionar uma entrada para o utilizador próprio, uma entrada para o grupo de propriedade e uma entrada para todos os outros utilizadores. Para saber mais sobre o utilizador próprio, o grupo de propriedade, e todos os outros utilizadores, consulte [Utilizadores e identidades.](data-lake-storage-access-control.md#users-and-identities)

Esta secção mostra-lhe como:

- Definir um ACL
- Definir ACLs recursivamente

### <a name="set-an-acl"></a>Definir um ACL

Utilize o `az storage fs access set` comando para definir o ACL de um **diretório**. 

Este exemplo define o ACL num diretório para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL à consola.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo define o ACL *predefinido* num diretório para o utilizador próprio, grupo de propriedade ou outros utilizadores, e depois imprime o ACL na consola.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Utilize o `az storage fs access set` comando para definir o acl de um **ficheiro**. 

Este exemplo define o ACL num ficheiro para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL à consola.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Para um conjunto da ACL de um grupo ou utilizador específico, utilize os respetivos IDs de objeto. Por exemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

A imagem a seguir mostra a saída após a definição do ACL de um ficheiro.

![Obtenha saída ACL 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o próprio utilizador e o grupo de proprietária apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Desacorda os ACLs de forma recorrente utilizando o comando [de acesso a az fs de acesso ao set-recursive.](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive)

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Estas entradas dão ao utilizador que lê, escreve e executa permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico com o objeto ID "xxxxxxx-xxxx-xxxx-xxxx-xxxx" ler e executar permissões.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se pretender definir uma entrada ACL **predefinida,** adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user::rwx` ou `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Atualizar ACLs

Quando *atualiza* um ACL, modifica o ACL em vez de substituir o ACL. Por exemplo, pode adicionar um novo princípio de segurança à ACL sem afetar outros princípios de segurança listados na ACL.  Para substituir o ACL em vez de atualizá-lo, consulte a secção [De Definição de ACLs](#set-acls) deste artigo.

Para atualizar um ACL, crie um novo objeto ACL com a entrada ACL que pretende atualizar e, em seguida, utilize esse objeto na atualização da operação ACL. Não obtenha o ACL existente, basta fornecer entradas ACL para serem atualizadas.

Esta secção mostra-lhe como:

- Atualizar um ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar um ACL

Outra forma de definir esta permissão é usar o `az storage fs access set` comando. 

Atualize o ACL de um diretório ou ficheiro definindo o `-permissions` parâmetro para a forma curta de um ACL.

Este exemplo atualiza o ACL de um **diretório**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo atualiza o ACL de um **ficheiro**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Para uma atualização do ACL de um grupo ou utilizador específico, utilize os respetivos IDs de objeto. Por exemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Também pode atualizar o utilizador e o grupo próprio de um diretório ou ficheiro, definindo os `--owner` parâmetros ou `group` parâmetros para o ID da entidade ou nome principal do utilizador (UPN) de um utilizador.

Este exemplo altera o proprietário de um diretório.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo altera o proprietário de um ficheiro. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Atualize os ACLs de forma recorrente utilizando o comando [de actualização-recursivo de acesso a az.](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive)

Este exemplo atualiza uma entrada ACL com permissão de escrita.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se pretender atualizar uma entrada ACL **predefinida,** adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Remover as entradas ACL recursivamente

Pode remover uma ou mais entradas ACL de forma recorrente. Para remover uma entrada ACL, crie um novo objeto ACL para a entrada ACL ser removida e, em seguida, utilize esse objeto na remoção da operação ACL. Não obtenha o ACL existente, basta fornecer as entradas ACL para serem removidas.

Remova as entradas ACL utilizando o [comando de remoção recursivo de armazenamento az fs.](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive)

Este exemplo remove uma entrada ACL do diretório de raiz do recipiente.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se pretender remover uma entrada ACL **predefinida,** adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão ao modificar os ACLs de forma recorrente. Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

Em caso de falha, pode devolver um token de continuação definindo o `--continue-on-failure` parâmetro para `false` . Depois de resolver os erros, pode retomar o processo a partir do ponto de falha, executando novamente o comando e, em seguida, definindo o `--continuation` parâmetro para o token de continuação.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Se quiser que o processo seja concluído ininterruptamente por erros de permissão, pode especificar isso.

Para assegurar que o processo esteja concluído ininterruptamente, desagure o `--continue-on-failure` parâmetro para `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ver também

- [Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Enviar comentários](https://github.com/Azure/azure-cli-extensions/issues)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)