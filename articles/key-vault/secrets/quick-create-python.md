---
title: Quickstart – Biblioteca de clientes Azure Key Vault Python – gerencie segredos
description: Aprenda a criar, recuperar e apagar segredos de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: f1f044eb3af35019eaf010e118bc4a5814269e9e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378560"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Quickstart: Azure Key Vault secreta biblioteca de clientes para Python

Começa com a biblioteca de clientes do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca de clientes Key Vault para Python para:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Criar e importar chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e a redundância global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize OS HSMs validados FIPS 140-2.

[Documentação de](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, 3.5.3, ou mais tarde
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

Este quickstart pressupõe que está a executar [o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) numa janela do terminal Linux.

## <a name="setting-up"></a>Configuração

### <a name="install-the-package"></a>Instale o pacote

A partir da janela da consola, instale a biblioteca de segredos Azure Key Vault para Python.

```console
pip install azure-keyvault-secrets
```

Para este arranque rápido, também terá de instalar o pacote azure.identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Criar um principal de serviço

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao serviço acesso principal ao seu cofre de chaves

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Definir variáveis ambientais

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para Python permite-lhe gerir chaves e ativos relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo.

A aplicação de amostra que demonstra operações semelhantes às mostradas neste artigo, bem como outras funcionalidades do Key Vault, está disponível no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets/samples).

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Autenticar o cofre e criar um cliente-chave depende das variáveis ambientais do [set, as variáveis ambientais](#set-environmental-variables) estão acima. O nome do seu cofre-chave é expandido para o cofre de chaves URI, no formato "https://<o seu nome de cofre-chave>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação é autenticada, pode colocar um segredo no seu keyvault usando o cliente. [método set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) Isto requer um nome para o segredo.  

```python
client.set_secret(secretName, secretValue)
```

Pode verificar se o segredo foi definido com o comando [secreto az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o [método get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-).

```python
retrieved_secret = client.get_secret(secretName)
 ```

O teu segredo está agora guardado `retrieved_secret.value` como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o [método begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-).

```python
client.begin_delete_secret(secretName)
```

Pode verificar se o segredo se foi com o comando secreto do [programa az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

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

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.begin_delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criaste um cofre, armazenaste um segredo e recuperaste esse segredo. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [As melhores práticas do Azure Key Vault](../general/best-practices.md)
