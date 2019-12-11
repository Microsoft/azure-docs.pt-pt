---
title: Guia de início rápido – biblioteca de cliente Azure Key Vault para Python
description: Saiba como criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de cliente do Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: aeac3b5ab1894e4392152ff255d582c338ceff18
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972430"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Início rápido: biblioteca de cliente Azure Key Vault para Python

Introdução à biblioteca de cliente Azure Key Vault para Python. Siga as etapas abaixo para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Use a biblioteca de cliente Key Vault para Python para:

- Aumente a segurança e o controle sobre chaves e senhas.
- Crie e importe chaves de criptografia em minutos.
- Reduza a latência com escala de nuvem e redundância global.
- Simplifique e automatize tarefas para certificados SSL/TLS.
- Use os HSMs validados pelo FIPS 140-2 nível 2.

[Documentação de referência de API](/python/api/overview/azure/key-vault?view=azure-python) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [pacote (índice de pacote do Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 ou posterior
- [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este início rápido pressupõe que você está executando [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) em uma janela de terminal do Linux.

## <a name="setting-up"></a>Configurando

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de segredos Azure Key Vault para Python.

```console
pip install azure-keyvault-secrets
```

Para este guia de início rápido, você precisará instalar o pacote Azure. Identity também:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

Este guia de início rápido usa um cofre de chaves do Azure criado previamente. Você pode criar um cofre de chaves seguindo as etapas na guia de início rápido [CLI do Azure](quick-create-cli.md), guia de início rápido [Azure PowerShell](quick-create-powershell.md)ou [portal do Azure início rápido](quick-create-portal.md). Como alternativa, você pode executar os comandos de CLI do Azure abaixo.

> [!Important]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua < seu-Unique-keyvault-Name > pelo nome do seu cofre de chaves nos exemplos a seguir.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A maneira mais simples de autenticar um aplicativo .NET baseado em nuvem é com uma identidade gerenciada; consulte [usar uma identidade gerenciada do serviço de aplicativo para acessar Azure Key Vault](managed-identity.md) para obter detalhes. Para simplificar, no entanto, este guia de início rápido cria um aplicativo de console .NET. A autenticação de um aplicativo de área de trabalho com o Azure requer o uso de uma entidade de serviço e uma política de controle de acesso.

Crie uma entidade de serviço usando o comando CLI do Azure [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Essa operação retornará uma série de pares de chave/valor. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Anote o clientId e o clientSecret, pois eles serão usados na etapa [definir variável de ambiente](#set-environmental-variables) abaixo.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que concede permissão para sua entidade de serviço passando o clientId para o comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Conceda à entidade de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Definir variáveis de ambiente

O método DefaultAzureCredential em nosso aplicativo depende de três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. Defina essas variáveis para os valores clientId, clientSecret e tenantid que você anotou na etapa [criar uma entidade de serviço](#create-a-service-principal) usando o formato `export VARNAME=VALUE`. (Esse método define apenas as variáveis para o shell atual e os processos criados a partir do Shell; para adicionar permanentemente essas variáveis ao seu ambiente, edite seu arquivo de `/etc/environment `.) 

Você também precisará salvar o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de cliente Azure Key Vault para Python permite que você gerencie chaves e ativos relacionados, como certificados e segredos. Os exemplos de código abaixo mostrarão como criar um cliente, definir um segredo, recuperar um segredo e excluir um segredo.

Todo o aplicativo de console está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior do seu código:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

A autenticação no cofre de chaves e a criação de um cliente de cofre de chaves dependem das variáveis ambientais na etapa [definir variáveis de ambiente](#set-environmental-variables) acima. O nome do cofre de chaves é expandido para o URI do Key Vault, no formato "https://< your-key-Vault-Name >. Vault. Azure. net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que seu aplicativo está autenticado, você pode colocar um segredo em seu cofre de chaves usando o cliente. Método setsecret] (/dotNet/API/Microsoft.Azure.keyvault.keyvaultclientextensions.setsecretasync) requer um nome para o segredo – estamos usando "MySecret" neste exemplo.  

```python
client.set_secret(secretName, secretValue);
```

Você pode verificar se o segredo foi definido com o comando [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora você pode recuperar o valor definido anteriormente com o [cliente. Método getsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Seu segredo agora está salvo como `retrieved_secret.value`.

### <a name="delete-a-secret"></a>Eliminar um segredo

Por fim, vamos excluir o segredo do seu cofre de chaves com o [cliente. Método DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName);
```

Você pode verificar se o segredo desapareceu com o comando [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o CLI do Azure ou Azure PowerShell para remover o cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"];
KVUri = "https://" + keyVaultName + ".vault.azure.net";

credential = DefaultAzureCredential()
client = SecretClient(vault_endpoint=KVUri, credential=credential)

secretName = "mySecret";

print("Input the value of your secret > ");
secretValue = raw_input();

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

client.set_secret(secretName, secretValue);

print(" done.");

print("Forgetting your secret.");
secretValue = "";
print("Your secret is '" + secretValue + "'.");

print("Retrieving your secret from " + keyVaultName + ".");

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.");
print("Deleting your secret from " + keyVaultName + " ...");

client.delete_secret(secretName);

print(" done.");
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Para saber mais sobre Key Vault e como integrá-lo a seus aplicativos, continue nos artigos abaixo.

- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Consulte o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)