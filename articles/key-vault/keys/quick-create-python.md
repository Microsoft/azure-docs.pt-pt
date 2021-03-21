---
title: Quickstart – Biblioteca cliente Azure Key Vault Python – gerencie as teclas
description: Aprenda a criar, recuperar e apagar chaves de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 7d1e4018382d26e4df289838ffbb03f1c87505e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935024"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Quickstart: Azure Key Vault chaves biblioteca cliente para Python

Começa com a biblioteca de clientes do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Ao utilizar o Key Vault para armazenar chaves criptográficas, evita armazenar tais chaves no seu código, o que aumenta a segurança da sua aplicação.

[Documentação de](/python/api/overview/azure/keyvault-keys-readme)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+ ou 3.5.3+](/azure/developer/python/configure-local-development-environment)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este quickstart pressupõe que está a executar [o Azure CLI](/cli/azure/install-azure-cli) numa janela do terminal Linux.

## <a name="set-up-your-local-environment"></a>Configurar o ambiente local

Este quickstart está a utilizar a biblioteca Azure Identity com o Azure CLI para autenticar o utilizador nos Serviços Azure. Os desenvolvedores também podem usar o Visual Studio ou Visual Studio Code para autenticar as suas chamadas, para obter mais informações, ver [Autenticar o cliente com a biblioteca de clientes da Azure Identity](/java/api/overview/azure/identity-readme).

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
    pip install azure.identity
    ```


1. Instale a biblioteca do cliente chave do cofre chave:

    ```terminal
    pip install azure-keyvault-keys
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

## <a name="create-the-sample-code"></a>Criar o código de amostra

A biblioteca de clientes chave Azure Key Vault para Python permite-lhe gerir chaves criptográficas. A seguinte amostra de código demonstra como criar um cliente, definir uma chave, recuperar uma chave e apagar uma chave.

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

- Se encontrar permissões, certifique-se de que executou o [ `az keyvault set-policy` comando](#grant-access-to-your-key-vault).
- A recorridação do código com o mesmo nome-chave pode produzir o erro, "(Conflito) A chave <name> está atualmente num estado eliminado, mas recuperável." Use um nome chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe  ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Guarde uma chave

Uma vez obtido o objeto cliente para o cofre de chaves, pode armazenar uma chave usando o método [create_rsa_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Também pode utilizar [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) ou [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

Chamar um `create` método gera uma chamada para a API Azure REST para o cofre de chaves.

Ao lidar com o pedido, a Azure autentica a identidade do autor da chamada (o principal do serviço) utilizando o objeto de credencial que forneceu ao cliente.

## <a name="retrieve-a-key"></a>Recuperar uma chave

Para ler uma chave do Key Vault, utilize o método [get_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-)

```python
retrieved_key = client.get_key(keyName)
 ```

Também pode verificar se a chave foi definida com o show de chaves Azure CLI [keyvault](/cli/azure/keyvault/key?#az-keyvault-key-show).

### <a name="delete-a-key"></a>Eliminar uma chave

Para eliminar uma chave, utilize o método [begin_delete_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-)

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

O `begin_delete_key` método é assíncrona e devolve um objeto poller. Chamar o método do poller `result` espera pela sua conclusão.

Pode verificar se a chave é eliminada com o [programa de teclas](/cli/azure/keyvault/key?#az-keyvault-key-show)Azure CLI .

Uma vez eliminada, uma chave permanece em estado apagado, mas recuperável por um tempo. Se voltar a executar o código, utilize um nome chave diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se também quiser experimentar certificados e [segredos,](../certificates/quick-create-python.md) pode reutilizar o Key Vault criado neste artigo. [](../secrets/quick-create-python.md)

Caso contrário, quando terminar com os recursos criados neste artigo, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Acesso seguro a um cofre de chaves](../general/secure-your-key-vault.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [Visão geral da segurança do Cofre de Chaves](../general/security-overview.md)
- [Autenticar com cofre de chaves](../general/authentication.md)