---
title: Quickstart - Biblioteca de clientes Azure Key Vault para JavaScript (v4)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 045589d3b1f0e376eaf854562d271a4483702997
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047901"
---
# <a name="quickstart-azure-key-vault-client-library-for-javascript-v4"></a>Quickstart: Biblioteca de clientes Azure Key Vault para JavaScript (v4)

Começa com a biblioteca secreta do Azure Key Vault para o JavaScript. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

[Documentação de](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Node.js](https://nodejs.org) atual para o seu sistema operativo.
- [CLI do Azure](/cli/azure/install-azure-cli)

Este quickstart pressupõe que está a executar [o Azure CLI](/cli/azure/install-azure-cli) numa janela do terminal Linux.

## <a name="setting-up"></a>Configuração
Este quickstart está a utilizar a biblioteca Azure Identity com o Azure CLI para autenticar o utilizador nos Serviços Azure. Os desenvolvedores também podem usar o Visual Studio ou Visual Studio Code para autenticar as suas chamadas, para obter mais informações, ver [Autenticar o cliente com a biblioteca de clientes da Azure Identity](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

### <a name="install-the-package"></a>Instale o pacote

A partir da janela da consola, instale a biblioteca de segredos Azure Key Vault para Node.js.

```console
npm install @azure/keyvault-secrets
```

Para este arranque rápido, também terá de instalar o pacote azure.identity:

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o seu cofre-chave que concede permissão secreta à sua conta de utilizador.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Esta aplicação está a usar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca secreta do Azure Key Vault para JavaScript permite-lhe gerir segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe  ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação é autenticada, pode colocar um segredo no seu keyvault usando o [método cliente.setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?#setsecret-string--string--setsecretoptions-) Isto requer um nome para o segredo - estamos usando "mySecret" nesta amostra.  

```javascript
await client.setSecret(secretName, secretValue);
```

Pode verificar se o segredo foi definido com o comando [secreto az keyvault:](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Pode agora recuperar o valor previamente definido com o [método cliente.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

O teu segredo está agora guardado `retrievedSecret.value` como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o [método de Nomeias.](/javascript/api/@azure/keyvault-secrets/secretclient?#begindeletesecret-string--begindeletesecretoptions-)

```javascript
await client.beginDeleteSecret(secretName)
```

Pode verificar se o segredo se foi com o comando secreto do [programa az keyvault:](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
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

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um cofre, armazenaste um segredo e recuperaste esse segredo. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Como [garantir o acesso a um cofre chave](../general/secure-your-key-vault.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)