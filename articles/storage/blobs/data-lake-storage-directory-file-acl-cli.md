---
title: Utilize o Azure CLI para gerir dados (Azure Data Lake Storage Gen2)
description: Utilize o CLI Azure para gerir diretórios e ficheiros em contas de armazenamento que tenham um espaço hierárquico de nomes.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650359"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Use o Azure CLI para gerir diretórios e ficheiros em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar a [Interface Command-Line (CLI)](/cli/azure/) para criar e gerir diretórios e ficheiros em contas de armazenamento que têm um espaço hierárquico de nomes.

Para saber como obter, definir e atualizar as listas de controlo de acesso (ACL) de diretórios e ficheiros, consulte [Use Azure CLI para gerir ACLs em Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Dar feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.6.0` ou superior.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Certifique-se de que tem a versão correta do Azure CLI instalada

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou](/cli/azure/install-azure-cli) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Verifique se a versão do Azure CLI que foi instalada é `2.6.0` ou superior utilizando o seguinte comando.

   ```azurecli
    az --version
   ```

   Se a sua versão do Azure CLI for inferior `2.6.0` a , então instale uma versão posterior. Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli).

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

## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um usando o `az storage fs create` comando. 

Este exemplo cria um recipiente chamado `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Mostrar propriedades de contentores

Pode imprimir as propriedades de um recipiente à consola utilizando o `az storage fs show` comando.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Listar o conteúdo do contentor

Listar o conteúdo de um diretório utilizando o `az storage fs file list` comando.

Este exemplo enumera o conteúdo de um contentor denominado `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Eliminar um contentor

Elimine um recipiente utilizando o `az storage fs delete` comando.

Este exemplo elimina um recipiente chamado `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório utilizando o `az storage fs directory create` comando. 

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor chamado que está localizado numa conta chamada `my-file-system` `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mostrar propriedades de diretório

Pode imprimir as propriedades de um diretório na consola utilizando o `az storage fs directory show` comando.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório utilizando o `az storage fs directory move` comando.

Este exemplo renomea um diretório do nome `my-directory` para o nome no mesmo `my-new-directory` recipiente.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Este exemplo move um diretório para um contentor chamado `my-second-file-system` .

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

Determinar se existe um diretório específico no recipiente utilizando o `az storage fs directory exists` comando.

Este exemplo revela se existe um diretório nomeado `my-directory` no `my-file-system` contentor. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório

Descarregue um ficheiro de um diretório usando o `az storage fs file download` comando.

Este exemplo descarrega um ficheiro chamado `upload.txt` de um diretório chamado `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Listar o conteúdo de um diretório utilizando o `az storage fs file list` comando.

Este exemplo enumera o conteúdo de um diretório nomeado `my-directory` que se encontra no contentor de uma conta de armazenamento `my-file-system` denominada `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Faça o upload de um ficheiro para um diretório utilizando o `az storage fs directory upload` comando.

Este exemplo envia um ficheiro nomeado `upload.txt` para um diretório chamado `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostrar propriedades de arquivo

Pode imprimir as propriedades de um ficheiro na consola utilizando o `az storage fs file show` comando.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Mudar de nome ou mover um ficheiro

Mude o nome ou mova um ficheiro utilizando o `az storage fs file move` comando.

Este exemplo renomea um ficheiro do nome `my-file.txt` para o nome `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Eliminar um ficheiro

Elimine um ficheiro utilizando o `az storage fs file delete` comando.

Este exemplo elimina um ficheiro chamado `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Ver também

- [Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Dar feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Use O CLI Azure para gerir ACLs em Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)