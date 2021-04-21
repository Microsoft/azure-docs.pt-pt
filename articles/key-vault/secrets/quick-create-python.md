---
title: Quickstart – Biblioteca de clientes Azure Key Vault Python – gerencie segredos
description: Aprenda a criar, recuperar e apagar segredos de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: d7b5144264b31e62eac43513d6a851d09a09c60f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814051"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Quickstart: Azure Key Vault biblioteca secreta de clientes para Python

Começa com a biblioteca secreta do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Ao utilizar o Key Vault para armazenar segredos, evita guardar segredos no seu código, o que aumenta a segurança da sua aplicação.

[Documentação de](/python/api/overview/azure/keyvault-secrets-readme)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+ ou 3.6+](/azure/developer/python/configure-local-development-environment)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este quickstart pressupõe que está a executar [o Azure CLI](/cli/azure/install-azure-cli) numa janela do terminal Linux.


## <a name="set-up-your-local-environment"></a>Configurar o ambiente local
Este quickstart está a utilizar a biblioteca Azure Identity com o Azure CLI para autenticar o utilizador nos Serviços Azure. Os desenvolvedores também podem usar o Visual Studio ou Visual Studio Code para autenticar as suas chamadas, para obter mais informações, ver [Autenticar o cliente com a biblioteca de clientes da Azure Identity](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

### <a name="install-the-packages"></a>Instalar as embalagens

1. Num terminal ou pedido de comando, crie uma pasta de projeto adequada e, em seguida, crie e ative um ambiente virtual Python como descrito em [ambientes virtuais Use Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Instalar a biblioteca de identidade do Diretório Ativo Azure:

    ```terminal
    pip install azure-identity
    ```


1. Instale a biblioteca de segredos do Cofre de Chaves:

    ```terminal
    pip install azure-keyvault-secrets
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o seu cofre-chave que concede permissão secreta à sua conta de utilizador.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Esta aplicação está a usar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Criar o código de amostra

A biblioteca secreta do Azure Key Vault para Python permite-lhe gerir segredos. A seguinte amostra de código demonstra como criar um cliente, estabelecer um segredo, recuperar um segredo e apagar um segredo.

Crie um ficheiro chamado *kv_secrets.py* que contenha este código.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Certifique-se de que o código na secção anterior está num ficheiro denominado *kv_secrets.py*. Em seguida, executar o código com o seguinte comando:

```terminal
python kv_secrets.py
```

- Se encontrar permissões, certifique-se de que executou o [ `az keyvault set-policy` comando](#grant-access-to-your-key-vault).
- Re-executar o código com o mesmo nome secreto pode produzir o erro, "(Conflito) Segredo <name> está atualmente em um estado apagado, mas recuperável." Use um nome secreto diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe  ['DefaultAzureCredential()'](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvar um segredo

Assim que tiver obtido o objeto do cliente para o cofre da chave, pode armazenar um segredo utilizando o método [set_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) 

```python
client.set_secret(secretName, secretValue)
```

A chamada `set_secret` gera uma chamada para a AZure REST API para o cofre chave.

Ao lidar com o pedido, a Azure autentica a identidade do autor da chamada (o principal do serviço) utilizando o objeto de credencial que forneceu ao cliente.

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Para ler um segredo do Key Vault, use o método [get_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-)

```python
retrieved_secret = client.get_secret(secretName)
 ```

O valor secreto está contido em `retrieved_secret.value` .

Você também pode recuperar um segredo com o azure CLI comando [az keyvault show secreto](/cli/azure/keyvault/secret?#az_keyvault_secret_show).

### <a name="delete-a-secret"></a>Eliminar um segredo

Para eliminar um segredo, utilize o método [begin_delete_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-)

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

O `begin_delete_secret` método é assíncrona e devolve um objeto poller. Chamar o método do poller `result` espera pela sua conclusão.

Pode verificar se o segredo foi removido com o comando Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show).

Uma vez apagado, um segredo permanece em estado apagado, mas recuperável por um tempo. Se voltar a executar o código, use um nome secreto diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se também quiser experimentar [certificados](../certificates/quick-create-python.md) e [chaves,](../keys/quick-create-python.md)pode reutilizar o Cofre-Chave criado neste artigo.

Caso contrário, quando terminar com os recursos criados neste artigo, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Acesso seguro a um cofre de chaves](../general/security-features.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [Visão geral da segurança do Cofre de Chaves](../general/security-features.md)
- [Autenticar com cofre de chaves](../general/authentication.md)
