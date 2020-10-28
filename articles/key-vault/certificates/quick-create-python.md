---
title: Quickstart – Biblioteca cliente Azure Key Vault Python – gerencie certificados
description: Saiba como criar, recuperar e apagar certificados de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: a5ba1699e082741db851ee2af3eb688ea8aa7843
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748063"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Quickstart: Azure Key Vault certificate client library for Python

Começa com a biblioteca de clientes do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Ao utilizar o Key Vault para armazenar certificados, evita armazenar certificados no seu código, o que aumenta a segurança da sua aplicação.

[Documentação de](/python/api/overview/azure/keyvault-certificates-readme)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+ ou 3.5.3+](https://docs.microsoft.com/azure/developer/python/configure-local-development-environment)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este quickstart pressupõe que está a executar [o Azure CLI](/cli/azure/install-azure-cli) numa janela do terminal Linux.

## <a name="set-up-your-local-environment"></a>Configurar o seu ambiente local

Este quickstart está a utilizar a biblioteca Azure Identity com o Azure CLI para autenticar o utilizador nos Serviços Azure. Os desenvolvedores também podem usar Visual Studio ou Visual Studio Code para autenticar as suas chamadas, para mais informações, ver [Autenticar o cliente com a biblioteca de clientes da Azure Identity](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

### <a name="install-the-packages"></a>Instalar as embalagens

1. Num terminal ou pedido de comando, crie uma pasta de projeto adequada e, em seguida, crie e ative um ambiente virtual Python como descrito em [ambientes virtuais Use Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Instalar a biblioteca de identidade do Diretório Ativo Azure:

    ```terminal
    pip install azure.identity
    ```


1. Instale a biblioteca do cliente do certificado Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o cofre-chave que concede permissão secreta à sua conta de utilizador

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

## <a name="create-the-sample-code"></a>Criar o código de amostra

A biblioteca de clientes do Azure Key Vault para Python permite-lhe gerir certificados. A seguinte amostra de código demonstra como criar um cliente, definir um certificado, recuperar um certificado e apagar um certificado.

Crie um ficheiro chamado *kv_certificates.py* que contenha este código.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Certifique-se de que o código na secção anterior está num ficheiro denominado *kv_certificates.py* . Em seguida, executar o código com o seguinte comando:

```terminal
python kv_certificates.py
```

- Se encontrar permissões, certifique-se de que executou o [ `az keyvault set-policy` comando](#grant-access-to-your-key-vault).
- A ree execução do código com o mesmo nome-chave pode produzir o erro, "(Conflict) Certificate <name> encontra-se atualmente num estado eliminado, mas recuperável." Use um nome chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe  ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Guardar um certificado

Uma vez obtido o objeto cliente para o cofre de chaves, pode criar um certificado utilizando o método [begin_create_certificate:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Aqui, o certificado requer uma política obtida com o método [CertificatePolicy.get_default.](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--)

Chamar um `begin_create_certificate` método gera uma chamada assíncrona para a API AZURE REST para o cofre de chaves. A chamada assíncrona devolve um objeto poller. Para esperar pelo resultado da operação, ligue para o método do `result` poller.

Ao lidar com o pedido, a Azure autentica a identidade do autor da chamada (o principal do serviço) utilizando o objeto de credencial que forneceu ao cliente.


### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Para ler um certificado da Key Vault, utilize o método [get_certificate:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-)

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Pode também verificar se o certificado foi definido com o certificado de comando Azure CLI [az keyvault](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Apagar um certificado

Para eliminar um certificado, utilize o método [begin_delete_certificate:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-)

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

O `begin_delete_certificate` método é assíncrona e devolve um objeto poller. Chamar o método do poller `result` espera pela sua conclusão.

Pode verificar se o certificado é eliminado com o certificado de comando Azure CLI [az keyvault](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

Uma vez eliminado, um certificado permanece em estado de aussitado mas recuperável por um tempo. Se voltar a executar o código, utilize um nome de certificado diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se também quiser experimentar [segredos](../secrets/quick-create-python.md) e [chaves,](../keys/quick-create-python.md)pode reutilizar o Key Vault criado neste artigo.

Caso contrário, quando terminar com os recursos criados neste artigo, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Acesso seguro a um cofre de chaves](../general/secure-your-key-vault.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [As melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com cofre de chaves](../general/authentication.md)
