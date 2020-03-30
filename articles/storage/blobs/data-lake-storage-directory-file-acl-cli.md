---
title: Utilize o Azure CLI para ficheiros & ACLs em Azure Data Lake Storage Gen2 (pré-visualização)
description: Utilize o Azure CLI para gerir diretórios e listas de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham um espaço de nome hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486139"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Utilize o Azure CLI para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2 (pré-visualização)

Este artigo mostra-lhe como usar a Interface de [Linha de Comando Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que tenham um espaço de nome hierárquico. 

> [!IMPORTANT]
> A `storage-preview` extensão que está em destaque neste artigo está atualmente em pré-visualização pública.

[Amostra](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 para mapeamento](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | Gen2[Dar feedback](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Versão Azure `2.0.67` CLI ou superior.

## <a name="install-the-storage-cli-extension"></a>Instale a extensão CLI de armazenamento

1. Abra a [Shell Azure Cloud](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), ou se [instalou](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Verifique se a versão do Azure CLI que foi instalada é `2.0.67` ou superior utilizando o seguinte comando.

   ```azurecli
    az --version
   ```
   Se a sua versão do Azure CLI for inferior `2.0.67`à , então instale uma versão posterior. Ver [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Instale a extensão `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Ligar à conta

1. Se estiver a utilizar o Azure CLI localmente, execute o comando de login.

   ```azurecli
   az login
   ```

   Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de entrada azure.

   Caso contrário, abra uma [https://aka.ms/devicelogin](https://aka.ms/devicelogin) página do navegador e introduza o código de autorização apresentado no seu terminal. Em seguida, inscreva-se com as credenciais da sua conta no navegador.

   Para saber mais sobre os diferentes métodos de autenticação, veja Iniciar sessão com a CLI do Azure.

2. Se a sua identidade estiver associada a mais de uma subscrição, então detete a sua subscrição ativa para a subscrição da conta de armazenamento que irá acolher o seu website estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua `<subscription-id>` o valor do espaço reservado pela identificação da sua subscrição.

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um usando `az storage container create` o comando. 

Este exemplo cria um `my-file-system`sistema de ficheiros chamado .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de `az storage blob directory create` diretório utilizando o comando. 

Este exemplo adiciona um `my-directory` diretório nomeado `my-file-system` para um sistema `mystorageaccount`de ficheiros chamado que está localizado numa conta chamada .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mostrar propriedades de diretório

Pode imprimir as propriedades de um diretório `az storage blob show` para a consola utilizando o comando.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova `az storage blob directory move` um diretório utilizando o comando.

Este exemplo renomeia um `my-directory` diretório `my-new-directory`do nome para o nome .

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório `az storage blob directory delete` utilizando o comando.

Este exemplo elimina um `my-directory`diretório chamado . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Verifique se existe um diretório

Determine se existe um diretório específico no `az storage blob directory exist` sistema de ficheiros utilizando o comando.

Este exemplo revela se existe `my-directory` um `my-file-system` diretório nomeado no sistema de ficheiros. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Faça o download de um `az storage blob directory download` ficheiro de um diretório utilizando o comando.

Este exemplo descarrega `upload.txt` um ficheiro chamado `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Este exemplo descarrega um diretório inteiro.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Enumera rumite o conteúdo `az storage blob directory list` de um diretório utilizando o comando.

Este exemplo lista o conteúdo `my-directory` de um diretório nomeado que está localizado no sistema de `my-file-system` ficheiros de uma conta de armazenamento chamada `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Faça o upload de um `az storage blob directory upload` ficheiro para um diretório usando o comando.

Este exemplo envia um `upload.txt` ficheiro nomeado `my-directory`para um diretório chamado . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Este exemplo envia um diretório inteiro.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Mostrar propriedades de ficheiros

Pode imprimir as propriedades de um ficheiro `az storage blob show` para a consola utilizando o comando.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Mudar o nome ou mover um ficheiro

Mude o nome ou mova `az storage blob move` um ficheiro utilizando o comando.

Este exemplo renomea um `my-file.txt` ficheiro do `my-file-renamed.txt`nome para o nome .

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Eliminar um ficheiro

Elimine um ficheiro `az storage blob delete` utilizando o comando.

Este exemplo elimina um ficheiro chamado`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gerir permissões

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar comandos, certifique-se de que o seu diretor de segurança foi atribuído ao papel de [Proprietário de Dados de Armazenamento Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Obtenha permissões de diretório e arquivo

Obtenha o ACL de um `az storage blob directory access show` **diretório** usando o comando.

Este exemplo obtém o ACL de um diretório, e depois imprime o ACL para a consola.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Obtenha as permissões **file** de acesso `az storage blob access show` de um ficheiro usando o comando. 

Este exemplo obtém o ACL de um ficheiro e depois imprime o ACL para a consola.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

A imagem seguinte mostra a saída depois de obter o ACL de um diretório.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o utilizador próprio leu, escreve e executa permissões. O grupo próprio só leu e executou permissões. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Definir diretórios e permissões de ficheiros

Utilize `az storage blob directory access set` o comando para definir o ACL de um **diretório**. 

Este exemplo define o ACL num diretório para o utilizador próprio, grupo de possuir ou outros utilizadores, e depois imprime o ACL para a consola.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo define o ACL *padrão* num diretório para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Utilize `az storage blob access set` o comando para definir a acl de um **ficheiro**. 

Este exemplo define o ACL num ficheiro para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
A imagem seguinte mostra a saída após a definição do ACL de um ficheiro.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o utilizador próprio e o grupo de possuir apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Atualizar diretórios e permissões de ficheiros

Outra forma de definir esta `az storage blob directory access update` permissão é usar o ou `az storage blob access update` comando. 

Atualize o ACL de um diretório ou ficheiro, definindo o `-permissions` parâmetro na forma curta de um ACL.

Este exemplo atualiza o ACL de um **diretório.**

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo atualiza o ACL de um **ficheiro**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Também pode atualizar o utilizador e o grupo próprio de `--owner` `group` um diretório ou ficheiro, definindo os ou parâmetros para o ID da entidade ou nome principal do utilizador (UPN) de um utilizador. 

Este exemplo muda o proprietário de um diretório. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo altera o proprietário de um ficheiro. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gerir metadados definidos pelo utilizador

Pode adicionar metadados definidos pelo utilizador a um `az storage blob directory metadata update` ficheiro ou diretório utilizando o comando com um ou mais pares de valor de nome.

Este exemplo adiciona metadados definidos pelo `my-directory` utilizador para um diretório nomeado.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Este exemplo mostra todos os metadados `my-directory`definidos pelo utilizador para o diretório nomeado .

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Consulte também

* [Amostra](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 para mapeamento Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Enviar comentários](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Código fonte](https://github.com/Azure/azure-cli-extensions/tree/master/src)

