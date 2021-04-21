---
title: Quickstart - Biblioteca de clientes chave Azure Key Vault para JavaScript (versão 4)
description: Saiba como criar, recuperar e apagar chaves de um cofre de chaves Azure utilizando a biblioteca de clientes JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: c0066409732f4492186ea0bf604261e1ab59ca9f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750307"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Quickstart: Biblioteca de clientes chave Azure Key Vault para JavaScript (versão 4)

Começa com a biblioteca de clientes chave Azure Key Vault para o JavaScript. [Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para chaves criptográficas. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, aprende-se a criar, recuperar e apagar chaves de um cofre de chaves Azure utilizando a biblioteca de clientes chave JavaScript

Recursos da biblioteca do cliente Key Vault:

[Documentação de](/javascript/api/overview/azure/key-vault-index)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Para obter mais informações sobre o Cofre-Chaves e as chaves, consulte:
- [Visão geral do cofre de chaves](../general/overview.md)
- [Visão geral das chaves](about-keys.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Node.js](https://nodejs.org) atual para o seu sistema operativo.
- [CLI do Azure](/cli/azure/install-azure-cli)
- Um Cofre chave - você pode criar um usando [o portal Azure](../general/quick-create-portal.md) [CLI](../general/quick-create-cli.md), ou [Azure PowerShell](../general/quick-create-powershell.md)

Este quickstart pressupõe que está a executar [O Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

## <a name="create-new-nodejs-application"></a>Criar nova aplicação de Node.js

Em seguida, crie uma aplicação Node.js que pode ser implantada na Cloud. 

1. Numa concha de comando, crie uma pasta `key-vault-node-app` chamada:

```azurecli
mkdir key-vault-node-app
```

1. Mude para o *recém-criado diretório de aplicações de nó-chave-cofre-node* e executar o comando 'init' para inicializar o projeto do nó:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Instalar pacotes de cofre de chaves

A partir da janela da consola, instale a biblioteca de [chaves](https://www.npmjs.com/package/@azure/keyvault-keys) Azure Key Vault para Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Instale o pacote [azure.identity](https://www.npmjs.com/package/@azure/identity) para autenticar num Cofre de Chaves

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Definir variáveis de ambiente

Esta aplicação está a usar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o cofre-chave que concede permissões chave à sua conta de utilizador

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Exemplos de código

As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir uma chave, recuperar uma chave e apagar uma chave. 

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

1. Crie um novo ficheiro de texto e guarde-o como 'index.js'

1. Adicionar chamadas necessárias para carregar módulos Azure e Node.js

1. Criar a estrutura para o programa, incluindo o manuseamento básico de exceções

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a utilizar a classe ['DefaultAzureCredential)'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) da [Azure Identity Library,](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)que permite utilizar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Mais informações sobre a autenticação no cofre da chave, consulte [o Guia do Desenvolvedor.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Adicione o seguinte código à função 'main()»

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Guarde uma chave

Agora que a sua aplicação é autenticada, pode colocar uma chave no seu teclado utilizando o [método createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) Os parâmetros do método aceitam um nome chave e o [tipo de chave](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Recuperar uma chave

Agora pode recuperar o valor previamente definido com o [método getKey](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Eliminar uma chave

Finalmente, vamos apagar e limpar a chave do seu cofre-chave com os métodos [iniciantesDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) e [purgadkey.](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_)

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Código de exemplo

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testar e verificar

Execute os seguintes comandos para executar a aplicação.

```azurecli
npm install
npm index.js
```

É apresentada uma variação da seguinte saída:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um cofre chave, armazenaste uma chave e recuperaste essa chave. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Leia uma [visão geral das chaves Azure Key Vault Keys](about-keys.md)
- Como [garantir o acesso a um cofre chave](../general/security-overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)
