---
title: Utilize o Azure CLI para ficheiros & ACLs em Azure Data Lake Storage Gen2
description: Utilize o Azure CLI para gerir diretórios e listas de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham um espaço de nome hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120611"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Utilize o Azure CLI para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar a Interface de [Linha de Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que tenham um espaço de nome hierárquico. 

[Gen1 para mapeamento](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  |  Gen2 [Dar feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Versão Azure CLI `2.5.1` ou superior.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Certifique-se de que tem a versão correta do Azure CLI instalada

1. Abra a [Shell Azure Cloud](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se [instalou](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Verifique se a versão do Azure CLI que foi instalada é `2.5.1` ou superior utilizando o seguinte comando.

   ```azurecli
    az --version
   ```
   Se a sua versão do Azure CLI for inferior `2.5.1` à , então instale uma versão posterior. Ver [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Ligar à conta

1. Se estiver a utilizar o Azure CLI localmente, execute o comando de login.

   ```azurecli
   az login
   ```

   Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de entrada azure.

   Caso contrário, abra uma página do navegador e introduza o código de [https://aka.ms/devicelogin](https://aka.ms/devicelogin) autorização apresentado no seu terminal. Em seguida, inscreva-se com as credenciais da sua conta no navegador.

   Para saber mais sobre diferentes métodos de autenticação, consulte [Autorizar o acesso a dados blob ou fila com o Azure CLI](../common/authorize-data-operations-cli.md).

2. Se a sua identidade estiver associada a mais de uma subscrição, então detete a sua subscrição ativa para a subscrição da conta de armazenamento que irá acolher o seu website estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o valor do `<subscription-id>` espaço reservado pela identificação da sua subscrição.

> [!NOTE]
> O exemplo apresentado neste artigo mostra a autorização do Azure Ative Directory (AD). Para saber mais sobre os métodos de autorização, consulte [Autorizar o acesso a dados blob ou fila com o Azure CLI](../common/authorize-data-operations-cli.md).

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um usando o `az storage fs create` comando. 

Este exemplo cria um sistema de ficheiros chamado `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Mostrar propriedades do sistema de ficheiros

Pode imprimir as propriedades de um sistema de ficheiros para a consola utilizando o `az storage fs show` comando.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Conteúdo do sistema de ficheiros de listas

Enumera rumite o conteúdo de um diretório utilizando o `az storage fs file list` comando.

Este exemplo lista o conteúdo de um sistema de ficheiros chamado `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Eliminar um sistema de ficheiros

Elimine um sistema de ficheiros utilizando o `az storage fs delete` comando.

Este exemplo elimina um sistema de ficheiros chamado `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório utilizando o `az storage fs directory create` comando. 

Este exemplo adiciona um diretório nomeado para um sistema de `my-directory` ficheiros chamado `my-file-system` que está localizado numa conta chamada `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mostrar propriedades de diretório

Pode imprimir as propriedades de um diretório para a consola utilizando o `az storage fs directory show` comando.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova um diretório utilizando o `az storage fs directory move` comando.

Este exemplo renomeia um diretório do nome para o nome no mesmo sistema de `my-directory` `my-new-directory` ficheiros.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Este exemplo move um diretório para um sistema de ficheiros chamado `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório utilizando o `az storage fs directory delete` comando.

Este exemplo elimina um diretório chamado `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Verifique se existe um diretório

Determine se existe um diretório específico no sistema de ficheiros utilizando o `az storage fs directory exists` comando.

Este exemplo revela se existe um diretório nomeado `my-directory` no sistema de `my-file-system` ficheiros. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Faça o download de um ficheiro de um diretório utilizando o `az storage fs file download` comando.

Este exemplo descarrega um ficheiro chamado `upload.txt` `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Enumera rumite o conteúdo de um diretório utilizando o `az storage fs file list` comando.

Este exemplo lista o conteúdo de um diretório nomeado `my-directory` que está localizado no sistema de `my-file-system` ficheiros de uma conta de armazenamento chamada `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Faça o upload de um ficheiro para um diretório usando o `az storage fs directory upload` comando.

Este exemplo envia um ficheiro nomeado `upload.txt` para um diretório chamado `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostrar propriedades de ficheiros

Pode imprimir as propriedades de um ficheiro para a consola utilizando o `az storage fs file show` comando.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Mudar o nome ou mover um ficheiro

Mude o nome ou mova um ficheiro utilizando o `az storage fs file move` comando.

Este exemplo renomea um ficheiro do nome `my-file.txt` para o nome `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Eliminar um ficheiro

Elimine um ficheiro utilizando o `az storage fs file delete` comando.

Este exemplo elimina um ficheiro chamado`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Gerir permissões

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar comandos, certifique-se de que o seu diretor de segurança foi atribuído ao papel de [Proprietário de Dados de Armazenamento Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Obter um ACL

Obtenha o ACL de um **diretório** usando o `az storage fs access show` comando.

Este exemplo obtém o ACL de um diretório, e depois imprime o ACL para a consola.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Obtenha as permissões de acesso de um **ficheiro** usando o `az storage fs access show` comando. 

Este exemplo obtém o ACL de um ficheiro e depois imprime o ACL para a consola.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem seguinte mostra a saída depois de obter o ACL de um diretório.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o utilizador próprio leu, escreve e executa permissões. O grupo próprio só leu e executou permissões. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Definir um ACL

Utilize o `az storage fs access set` comando para definir o ACL de um **diretório**. 

Este exemplo define o ACL num diretório para o utilizador próprio, grupo de possuir ou outros utilizadores, e depois imprime o ACL para a consola.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo define o ACL *padrão* num diretório para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Utilize o `az storage fs access set` comando para definir a acl de um **ficheiro**. 

Este exemplo define o ACL num ficheiro para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem seguinte mostra a saída após a definição do ACL de um ficheiro.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o utilizador próprio e o grupo de possuir apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Atualizar um ACL

Outra forma de definir esta permissão é usar o `az storage fs access set` comando. 

Atualize o ACL de um diretório ou ficheiro, definindo o `-permissions` parâmetro na forma curta de um ACL.

Este exemplo atualiza o ACL de um **diretório.**

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo atualiza o ACL de um **ficheiro**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Também pode atualizar o utilizador e o grupo próprio de um diretório ou ficheiro, definindo os `--owner` `group` ou parâmetros para o ID da entidade ou nome principal do utilizador (UPN) de um utilizador. 

Este exemplo muda o proprietário de um diretório. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo altera o proprietário de um ficheiro. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Consulte também

* [Gen1 para mapeamento Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Enviar comentários](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


