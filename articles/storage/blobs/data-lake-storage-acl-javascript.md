---
title: Use o JavaScript para definir ACLs em Azure Data Lake Storage Gen2
description: Utilize a biblioteca de clientes do Azure Storage Data Lake para o JavaScript gerir as listas de controlo de acesso (ACL) em contas de armazenamento que tenham o espaço hierárquico (HNS) ativado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 4d3e13c6593c0e11df84131a9a07eb2868277d2f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654239"
---
# <a name="use-javascript-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Use o JavaScript para gerir ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o JavaScript para obter, definir e atualizar as listas de controlo de acesso de diretórios e ficheiros. 

[Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.6.0` ou superior.

- Uma das seguintes permissões de segurança:

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.  

  - Utilizador próprio do contentor-alvo ou diretório ao qual planeia aplicar as definições ACL. Para definir os ACLs de forma recorrente, isto inclui todos os itens para crianças no contentor-alvo ou diretório.
  
  - Chave da conta de armazenamento.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua alteração, consulte o modelo de controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Pode utilizar a biblioteca de [clientes de identidade Azure para jS](https://www.npmjs.com/package/@azure/identity) para autenticar a sua aplicação com Azure AD.

Arranja uma identificação com um cliente, um segredo de cliente, e uma identificação de inquilino. Para isso, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md) Como parte desse processo, terás de atribuir um dos seguintes papéis de [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ao teu diretor de segurança. 

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  

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

## <a name="get-and-set-a-directory-acl"></a>Obter e definir um diretório ACL

Este exemplo recebe e, em seguida, define o ACL de um diretório chamado `my-directory` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

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

### <a name="get-and-set-a-file-acl"></a>Obter e definir um arquivo ACL

Este exemplo recebe e, em seguida, define o ACL de um ficheiro chamado `upload-file.txt` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

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

## <a name="see-also"></a>Ver também

- [Pacote (Gestor de Pacotes de Nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)