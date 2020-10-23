---
title: Utilize o JavaScript para ficheiros & ACLs em Azure Data Lake Storage Gen2
description: Utilize a biblioteca de clientes do Azure Storage Data Lake para o JavaScript para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico de nomes (HNS) ativados.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 882a12838d13f511262486ff3adf332da32599c1
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131535"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o JavaScript para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o JavaScript para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico de nome (HNS) ativado. 

[Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Se estiver a utilizar este pacote numa aplicação Node.js, terá de Node.js 8.0.0 ou superior.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca do cliente do Data Lake para o JavaScript abrindo uma janela de terminal e, em seguida, digitando o seguinte comando.

```javascript
npm install @azure/storage-file-datalake
```

Importe o `storage-file-datalake` pacote colocando esta declaração no topo do seu ficheiro de código. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Ligar à conta 

Para utilizar os snippets neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Este método de autorização funciona apenas para Node.js pedidos. Se planeia executar o seu código num browser, pode autorizar utilizando o Azure Ative Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para jS](https://www.npmjs.com/package/@azure/identity) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://www.npmjs.com/package/@azure/identity) obter documentação JS.

## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um através de uma instância **FileSystemClient** e, em seguida, ligar para o método **FileSystemClient.Create.**

Este exemplo cria um recipiente chamado `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório obtendo uma instância **de Diretório** e, em seguida, chamando o método **DirectoryClient.create.**

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método **DirectoryClient.rename.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-diretório para o nome `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Este exemplo move um diretório nomeado `my-directory-renamed` para um sub-diretório de um diretório chamado `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório chamando o método **Diretório.delete.**

Este exemplo elimina um diretório chamado `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```



## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Primeiro, leia um ficheiro. Este exemplo utiliza o `fs` módulo Node.js. Em seguida, crie uma referência de ficheiro no directório-alvo criando uma instância **FileClient** e, em seguida, chamando o método **FileClient.create.** Faça o upload de um ficheiro chamando o método **FileClient.append.** Certifique-se de que completa o upload ligando para o método **FileClient.flush.**

Este exemplo envia um ficheiro de texto para um diretório chamado `my-directory` .'

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório

Em primeiro lugar, crie uma instância **FileSystemClient** que represente o ficheiro que pretende descarregar. Utilize o método **FileSystemClient.read** para ler o ficheiro. Então, escreva o ficheiro. Este exemplo utiliza o módulo Node.js `fs` para o fazer. 

> [!NOTE]
> Este método de descarregar um ficheiro funciona apenas para aplicações Node.js. Se planeia executar o seu código num browser, consulte a biblioteca de clientes do [Azure Storage File Data Lake para](https://www.npmjs.com/package/@azure/storage-file-datalake) o ficheiro de leitura JavaScript, para um exemplo de como fazê-lo num browser. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada diretório e arquivo que está localizado num diretório denominado `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="manage-access-control-lists-acls"></a>Gerir listas de controlo de acesso (ACLs)

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Este exemplo recebe e, em seguida, define o ACL de um diretório chamado `my-directory` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `/` () para o método **DataLakeFileSystemClient.getDirectoryClient.**

### <a name="manage-a-file-acl"></a>Gerir um ficheiro ACL

Este exemplo recebe e, em seguida, define o ACL de um ficheiro chamado `upload-file.txt` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Consulte também

* [Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)