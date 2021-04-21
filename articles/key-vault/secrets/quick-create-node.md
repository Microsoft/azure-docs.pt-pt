---
title: Quickstart - Biblioteca secreta do cliente Azure Key Vault para JavaScript (versão 4)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 90e505a4d505cc52920f1c7727afb321f4eda462
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814159"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Quickstart: Azure Key Vault biblioteca secreta de clientes para JavaScript (versão 4)

Começa com a biblioteca secreta do Azure Key Vault para o JavaScript. [Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para segredos. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, aprende-se a criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes JavaScript

Recursos da biblioteca do cliente Key Vault:

[Documentação de](/javascript/api/overview/azure/key-vault-index)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Para mais informações sobre o Cofre-Chave e segredos, consulte:
- [Visão geral do cofre de chaves](../general/overview.md)
- [Visão geral dos segredos.](about-secrets.md)

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

```terminal
mkdir key-vault-node-app
```

1. Mude para o *recém-criado diretório de aplicações de nó-chave-cofre-node* e executar o comando 'init' para inicializar o projeto do nó:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Instalar pacotes de cofre de chaves

A partir da janela da consola, instale a [biblioteca de segredos](https://www.npmjs.com/package/@azure/keyvault-secrets) Azure Key Vault para Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Instale o pacote [azure.identity](https://www.npmjs.com/package/@azure/identity) para autenticar num Cofre de Chaves

```terminal
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

Crie uma política de acesso para o seu cofre-chave que concede permissões secretas à sua conta de utilizador

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Exemplos de código

As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo. 

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
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a utilizar a classe ['DefaultAzureCredential)'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) da [Azure Identity Library,](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)que nos permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para obter mais informações sobre a autenticação no Cofre de Chaves, consulte [o Guia do Desenvolvedor.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Adicione o seguinte código à função 'main()»

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação é autenticada, pode colocar um segredo no seu keyvault usando o [método setSecret](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_) Isto requer um nome para o segredo - estamos usando "mySecret" nesta amostra.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o [método getSecret](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

O teu segredo está agora guardado `retrievedSecret.value` como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar e limpar o segredo do seu cofre-chave com os métodos [startDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) e [purpuredSecret.](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_)

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Código de exemplo

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

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
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testar e verificar

1. Execute os seguintes comandos para executar a aplicação.

    ```terminal
    npm install
    node index.js
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, o meu Código de Passagem.

    É apresentada uma variação da seguinte saída:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um cofre, armazenaste um segredo e recuperaste esse segredo. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Leia uma [visão geral dos segredos do cofre da chave Azure](about-secrets.md)
- Como [garantir o acesso a um cofre chave](../general/security-features.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-features.md)
