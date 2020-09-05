---
title: Quickstart – Biblioteca cliente Azure Key Vault Python – gerencie as teclas
description: Aprenda a criar, recuperar e apagar chaves de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482142"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Quickstart: Azure Key Vault chaves biblioteca cliente para Python

Começa com a biblioteca de clientes do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Ao utilizar o Key Vault para armazenar chaves criptográficas, evita armazenar tais chaves no seu código, o que aumenta a segurança da sua aplicação.

[Documentação de](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Configurar o seu ambiente local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instale a biblioteca de chaves key vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao serviço acesso principal ao seu cofre de chaves

Executar o seguinte comando [de definição de keyvault az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para autorizar o seu principal de serviço para eliminar, obter, listar e criar operações nas teclas. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[festa](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Este comando baseia-se nas `KEY_VAULT_NAME` `AZURE_CLIENT_ID` variáveis ambientais criadas em etapas anteriores.

Para mais informações, consulte [Atribuir uma política de acesso - CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Criar o código de amostra

A biblioteca de clientes Azure Key Vault para Python permite-lhe gerir chaves criptográficas e ativos relacionados, tais como certificados e segredos. A seguinte amostra de código demonstra como criar um cliente, estabelecer um segredo, recuperar um segredo e apagar um segredo.

Crie um ficheiro chamado *kv_keys.py* que contenha este código.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Certifique-se de que o código na secção anterior está num ficheiro denominado *kv_keys.py*. Em seguida, executar o código com o seguinte comando:

```terminal
python kv_keys.py
```

- Se encontrar permissões, certifique-se de que executou o [ `az keyvault set-policy` comando](#give-the-service-principal-access-to-your-key-vault).
- A recorridação do código com o mesmo nome-chave pode produzir o erro, "(Conflito) A chave <name> está atualmente num estado eliminado, mas recuperável." Use um nome chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

No código anterior, o [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objeto utiliza as variáveis ambientais que criou para o seu principal serviço. Fornece esta credencial sempre que cria um objeto cliente a partir de uma biblioteca Azure, [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python) como, juntamente com o URI do recurso com o quais pretende trabalhar através desse cliente:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Guarde uma chave

Uma vez obtido o objeto cliente para o cofre de chaves, pode armazenar uma chave usando o método [create_rsa_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Também pode utilizar [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) ou [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

Chamar um `create` método gera uma chamada para a API Azure REST para o cofre de chaves.

Ao lidar com o pedido, a Azure autentica a identidade do autor da chamada (o principal do serviço) utilizando o objeto de credencial que forneceu ao cliente.

Verifica igualmente se o autor da chamada está autorizado a executar a ação solicitada. Concedeu esta autorização ao diretor de serviço mais cedo usando o [ `az keyvault set-policy` comando.](#give-the-service-principal-access-to-your-key-vault)

## <a name="retrieve-a-key"></a>Recuperar uma chave

Para ler uma chave do Key Vault, utilize o método [get_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-)

```python
retrieved_key = client.get_key(keyName)
 ```

Também pode verificar se a chave foi definida com o show de chaves Azure CLI [keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

### <a name="delete-a-key"></a>Eliminar uma chave

Para eliminar uma chave, utilize o método [begin_delete_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-)

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

O `begin_delete_key` método é assíncrona e devolve um objeto poller. Chamar o método do poller `result` espera pela sua conclusão.

Pode verificar se a chave é eliminada com o [programa de teclas](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)Azure CLI .

Uma vez eliminada, uma chave permanece em estado apagado, mas recuperável por um tempo. Se voltar a executar o código, utilize um nome chave diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se também quiser experimentar certificados e [segredos,](../certificates/quick-create-python.md) pode reutilizar o Key Vault criado neste artigo. [secrets](../secrets/quick-create-python.md)

Caso contrário, quando terminar com os recursos criados neste artigo, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [As melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com cofre de chaves](../general/authentication.md)
