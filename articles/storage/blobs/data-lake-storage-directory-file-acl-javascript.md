---
title: Use o JavaScript para ficheiros & ACLs em Azure Data Lake Storage Gen2
description: Utilize a biblioteca de clientes do Lago de Dados de Armazenamento Azure para o JavaScript para gerir listas de controlo de diretórios e de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061544"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o JavaScript para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o JavaScript para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado. 

[Amostras](https://www.npmjs.com/package/@azure/storage-file-datalake) |  | de pacote (Gestor de Pacotes de Nó)[dão feedback](https://github.com/Azure/azure-sdk-for-java/issues) [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Se estiver a utilizar este pacote numa aplicação Node.js, vai precisar do Nó.js 8.0.0 ou superior.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca cliente data lake para JavaScript abrindo uma janela de terminal e, em seguida, digitando o seguinte comando.

```javascript
npm install @azure/storage-file-datalake
```

Importe `storage-file-datalake` o pacote colocando esta declaração no topo do seu ficheiro de código. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Ligar à conta 

Para utilizar os cortes neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se utilizando uma chave de conta

Esta é a maneira mais fácil de se ligar a uma conta. 

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
> Este método de autorização funciona apenas para aplicações node.js. Se planeia executar o seu código num browser, pode autorizar utilizando o Azure Ative Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Diretório Ativo Azure (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para a JS](https://www.npmjs.com/package/@azure/identity) autenticar a sua aplicação com a Azure AD.

Este exemplo cria uma instância **DataLakeServiceClient** utilizando um ID do cliente, um segredo de cliente e um ID de inquilino.  Para obter estes valores, consulte [Adquirir um símbolo da Azure AD para autorizar pedidos de uma aplicação de cliente](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de clientes de identidade Azure para obter documentação [js.](https://www.npmjs.com/package/@azure/identity)

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um obtendo uma instância do **FileSystemClient** e, em seguida, ligando para o método **FileSystemClient.Create.**

Este exemplo cria um `my-file-system`sistema de ficheiros chamado . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório obtendo uma instância **do Cliente do Diretório** e, em seguida, ligando para o método **DirectoryClient.create.**

Este exemplo adiciona um `my-directory` diretório nomeado para um sistema de ficheiros. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método **De nome Do Cliente.Rename.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-directório para o nome `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Este exemplo move um `my-directory-renamed` diretório nomeado para um sub-directório de um diretório chamado `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório ligando para o método **'Cliente'.**

Este exemplo elimina um `my-directory`diretório chamado .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Este exemplo recebe e, em seguida, `my-directory`define o ACL de um diretório chamado . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros acesso de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Primeiro, leia um ficheiro. Este exemplo utiliza o módulo `fs` Node.js. Em seguida, crie uma referência de ficheiro no diretório alvo, criando uma instância **fileClient** e, em seguida, chamando o método **FileClient.create.** Faça upload de um ficheiro ligando para o método **FileClient.append.** Certifique-se de que completa o upload ligando para o método **FileClient.flush.**

Este exemplo envia um ficheiro de `my-directory`texto para um diretório chamado .'

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

## <a name="manage-a-file-acl"></a>Gerir um ficheiro ACL

Este exemplo recebe e, em seguida, `upload-file.txt`define o ACL de um ficheiro chamado . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros acesso de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="download-from-a-directory"></a>Baixar de um diretório

Em primeiro lugar, crie uma instância **fileSystemClient** que represente o ficheiro que pretende descarregar. Utilize o método **FileSystemClient.read** para ler o ficheiro. Então, escreva o ficheiro. Este exemplo usa o módulo `fs` Node.js para o fazer. 

> [!NOTE]
> Este método de descarregar um ficheiro funciona apenas para aplicações Node.js. Se planeia executar o seu código num browser, consulte a biblioteca de clientes do Data Lake do [Arquivo de Armazenamento Azure para](https://www.npmjs.com/package/@azure/storage-file-datalake) o javaScript readme file para um exemplo de como fazê-lo num navegador. 

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

Este exemplo, imprime os nomes de cada diretório e `my-directory`ficheiro que está localizado num diretório chamado .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Consulte também

* [Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Dar Feedback](https://github.com/Azure/azure-sdk-for-java/issues)