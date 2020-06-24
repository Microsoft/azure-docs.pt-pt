---
title: Quickstart - Biblioteca de clientes Azure Key Vault para Node.js (v4)
description: Aprenda a criar, recuperar e apagar segredos de um cofre de chaves Azure usando a biblioteca de clientes Node.js
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 21add865818d73937aec241f8f60e20158202348
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125298"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Quickstart: Biblioteca de clientes Azure Key Vault para Node.js (v4)

Começa com a biblioteca de clientes do Azure Key Vault para Node.js. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca do cliente Key Vault para Node.js:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Criar e importar chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e a redundância global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize OS HSMs validados FIPS 140-2.

[Documentação de](/javascript/api/overview/azure/key-vault?view=azure-node-latest)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Node.js](https://nodejs.org) atual para o seu sistema operativo.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este quickstart pressupõe que está a executar [o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) numa janela do terminal Linux.

## <a name="setting-up"></a>Configuração

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

Este quickstart usa um cofre de chave Azure pré-criado. Pode criar um cofre-chave seguindo os passos no [quickstart Azure CLI,](quick-create-cli.md) [Azure PowerShell quickstart](quick-create-powershell.md), ou [Azure portal quickstart](quick-create-portal.md). Em alternativa, pode simplesmente executar os comandos Azure CLI abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A forma mais simples de autenticar uma aplicação baseada na nuvem é com uma identidade gerida; ver [Utilizar uma identidade gerida pelo Serviço de Aplicações para aceder ao Cofre da Chave Azure](../general/managed-identity.md) para obter mais detalhes.

No entanto, por uma questão de simplicidade, este quickstart cria uma aplicação para desktop, que requer a utilização de um principal de serviço e de uma política de controlo de acessos. O seu diretor de serviço requer um nome único no formato "http:// &lt; meu nome principal de serviço &gt; único".

Criar um principal de serviço utilizando o comando Azure CLI [ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Esta operação devolverá uma série de pares de chaves/valor. 

```azurecli
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Tome nota do clienteId e clienteSecret, pois vamos usá-los no passo [variável ambiental definido](#set-environmental-variables) abaixo.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao serviço acesso principal ao seu cofre de chaves

Crie uma política de acesso para o seu cofre-chave que concede permissão ao seu principal de serviço, passando o clienteId para o comando [de definição de chave-tevault az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Dê ao diretor de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Definir variáveis ambientais

O método DefaultAzureCredential na nossa aplicação baseia-se em três variáveis ambientais: `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET` , e `AZURE_TENANT_ID` . Desaprova estas variáveis para os valores clienteId, clientesSecret e tenantId que notou no [Create um](#create-a-service-principal) passo principal de serviço usando o `export VARNAME=VALUE` formato. (Isto apenas define as variáveis para a sua concha atual e processos criados a partir da concha; para adicionar permanentemente estas variáveis ao seu ambiente, edite o seu `/etc/environment ` ficheiro.) 

Também terá de guardar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para Node.js permite-lhe gerir chaves e bens relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo.

Toda a aplicação para consolas está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Autenticar o cofre-chave e criar um cliente-chave do cofre depende das variáveis ambientais do Conjunto de [variáveis ambientais,](#set-environmental-variables) passo acima, e do [construtor SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

O nome do seu cofre-chave é expandido para o cofre uri chave, no formato `https://<your-key-vault-name>.vault.azure.net` . 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação é autenticada, pode colocar um segredo no seu keyvault usando o método de segredo de [cliente.set](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) Isto requer um nome para o segredo.  

```javascript
await client.setSecret(secretName, secretValue);
```

Pode verificar se o segredo foi definido com o comando [secreto az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Pode agora recuperar o valor previamente definido com o [método cliente.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

O teu segredo está agora guardado `retrievedSecret.value` como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o [método de Nomeias.](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-)

```javascript
await client.beginDeleteSecret(secretName)
```

Pode verificar se o segredo se foi com o comando secreto do [programa az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

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

Neste arranque rápido criaste um cofre, armazenaste um segredo e recuperaste esse segredo. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)