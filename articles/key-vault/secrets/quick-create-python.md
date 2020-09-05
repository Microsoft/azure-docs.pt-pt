---
title: Quickstart – Biblioteca de clientes Azure Key Vault Python – gerencie segredos
description: Aprenda a criar, recuperar e apagar segredos de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489209"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Quickstart: Azure Key Vault secreta biblioteca de clientes para Python

Começa com a biblioteca de clientes do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Ao utilizar o Key Vault para armazenar segredos, evita guardar segredos no seu código, o que aumenta a segurança da sua aplicação.

[Documentação de](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Configurar o seu ambiente local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instale a biblioteca de segredos do Cofre de Chaves:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao serviço acesso principal ao seu cofre de chaves

Executar o seguinte comando [de definição de keyvault az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para autorizar o seu principal de serviço para obter, listar e definir operações em segredos. Este comando baseia-se nas `KEY_VAULT_NAME` `AZURE_CLIENT_ID` variáveis ambientais criadas em etapas anteriores.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[festa](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Este comando baseia-se nas `KEY_VAULT_NAME` `AZURE_CLIENT_ID` variáveis ambientais criadas em etapas anteriores.

Para mais informações, consulte [Atribuir uma política de acesso - CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Criar o código de amostra

A biblioteca de clientes Azure Key Vault para Python permite-lhe gerir segredos e ativos relacionados, tais como certificados e chaves criptográficas. A seguinte amostra de código demonstra como criar um cliente, estabelecer um segredo, recuperar um segredo e apagar um segredo.

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

- Se encontrar permissões, certifique-se de que executou o [ `az keyvault set-policy` comando](#give-the-service-principal-access-to-your-key-vault).
- Re-executar o código com o mesmo nome secreto pode produzir o erro, "(Conflito) Segredo <name> está atualmente em um estado apagado, mas recuperável." Use um nome secreto diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

No código anterior, o [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objeto utiliza as variáveis ambientais que criou para o seu principal serviço. Fornece esta credencial sempre que cria um objeto cliente a partir de uma biblioteca Azure, [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) como, juntamente com o URI do recurso com o quais pretende trabalhar através desse cliente:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvar um segredo

Assim que tiver obtido o objeto do cliente para o cofre da chave, pode armazenar um segredo utilizando o método [set_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) 

```python
client.set_secret(secretName, secretValue)
```

A chamada `set_secret` gera uma chamada para a AZure REST API para o cofre chave.

Ao lidar com o pedido, a Azure autentica a identidade do autor da chamada (o principal do serviço) utilizando o objeto de credencial que forneceu ao cliente.

Verifica igualmente se o autor da chamada está autorizado a executar a ação solicitada. Concedeu esta autorização ao diretor de serviço mais cedo usando o [ `az keyvault set-policy` comando.](#give-the-service-principal-access-to-your-key-vault)

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Para ler um segredo do Key Vault, use o método [get_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-)

```python
retrieved_secret = client.get_secret(secretName)
 ```

O valor secreto está contido em `retrieved_secret.value` .

Você também pode recuperar um segredo com o azure CLI comando [az keyvault show secreto](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Eliminar um segredo

Para eliminar um segredo, utilize o método [begin_delete_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-)

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

O `begin_delete_secret` método é assíncrona e devolve um objeto poller. Chamar o método do poller `result` espera pela sua conclusão.

Pode verificar se o segredo foi removido com o comando Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Uma vez apagado, um segredo permanece em estado apagado, mas recuperável por um tempo. Se voltar a executar o código, use um nome secreto diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se também quiser experimentar [certificados](../certificates/quick-create-python.md) e [chaves,](../keys/quick-create-python.md)pode reutilizar o Cofre-Chave criado neste artigo.

Caso contrário, quando terminar com os recursos criados neste artigo, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [As melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com cofre de chaves](../general/authentication.md)
