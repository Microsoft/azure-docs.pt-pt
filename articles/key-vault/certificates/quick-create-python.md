---
title: Quickstart – Biblioteca cliente Azure Key Vault Python – gerencie certificados
description: Saiba como criar, recuperar e apagar certificados de um cofre de chaves Azure usando a biblioteca do cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488631"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Quickstart: Azure Key Vault certifica biblioteca de clientes para Python

Começa com a biblioteca de clientes do Azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas. Ao utilizar o Key Vault para armazenar certificados, evita armazenar certificados no seu código, o que aumenta a segurança da sua aplicação.

[Documentação de](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Configurar o seu ambiente local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instale a biblioteca de certificados Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao serviço acesso principal ao seu cofre de chaves

Executar o seguinte comando [de definição de keyvault az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para autorizar o seu principal de serviço para obter, listar e criar operações em certificados.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[festa](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Este comando baseia-se nas `KEY_VAULT_NAME` `AZURE_CLIENT_ID` variáveis ambientais criadas em etapas anteriores.

Para mais informações, consulte [Atribuir uma política de acesso - CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Criar o código de amostra

A biblioteca de clientes Azure Key Vault para Python permite-lhe gerir certificados e bens relacionados, tais como segredos e chaves criptográficas. A seguinte amostra de código demonstra como criar um cliente, estabelecer um segredo, recuperar um segredo e apagar um segredo.

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

Certifique-se de que o código na secção anterior está num ficheiro denominado *kv_certificates.py*. Em seguida, executar o código com o seguinte comando:

```terminal
python kv_certificates.py
```

- Se encontrar permissões, certifique-se de que executou o [ `az keyvault set-policy` comando](#give-the-service-principal-access-to-your-key-vault).
- A ree execução do código com o mesmo nome-chave pode produzir o erro, "(Conflict) Certificate <name> encontra-se atualmente num estado eliminado, mas recuperável." Use um nome chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

No código anterior, o [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objeto utiliza as variáveis ambientais que criou para o seu principal serviço. Fornece esta credencial sempre que cria um objeto cliente a partir de uma biblioteca Azure, [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python) como, juntamente com o URI do recurso com o quais pretende trabalhar através desse cliente:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Guardar um certificado

Uma vez obtido o objeto cliente para o cofre de chaves, pode criar um certificado utilizando o método [begin_create_certificate:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Aqui, o certificado requer uma política obtida com o método [CertificatePolicy.get_default.](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--)

Chamar um `begin_create_certificate` método gera uma chamada assíncrona para a API AZURE REST para o cofre de chaves. A chamada assíncrona devolve um objeto poller. Para esperar pelo resultado da operação, ligue para o método do `result` poller.

Ao lidar com o pedido, a Azure autentica a identidade do autor da chamada (o principal do serviço) utilizando o objeto de credencial que forneceu ao cliente.

Verifica igualmente se o autor da chamada está autorizado a executar a ação solicitada. Concedeu esta autorização ao diretor de serviço mais cedo usando o [ `az keyvault set-policy` comando.](#give-the-service-principal-access-to-your-key-vault)

### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Para ler um certificado da Key Vault, utilize o método [get_certificate:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-)

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Pode também verificar se o certificado foi definido com o certificado de comando Azure CLI [az keyvault](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Apagar um certificado

Para eliminar um certificado, utilize o método [begin_delete_certificate:](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-)

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

O `begin_delete_certificate` método é assíncrona e devolve um objeto poller. Chamar o método do poller `result` espera pela sua conclusão.

Pode verificar se o certificado é eliminado com o certificado de comando Azure CLI [az keyvault](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

Uma vez eliminado, um certificado permanece em estado de aussitado mas recuperável por um tempo. Se voltar a executar o código, utilize um nome de certificado diferente.

## <a name="clean-up-resources"></a>Limpar recursos

Se também quiser experimentar [segredos](../secrets/quick-create-python.md) e [chaves,](../keys/quick-create-python.md)pode reutilizar o Key Vault criado neste artigo.

Caso contrário, quando terminar com os recursos criados neste artigo, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do cofre de chave do Azure](../general/overview.md)
- [Guia de desenvolvedores do Azure Key Vault](../general/developers-guide.md)
- [As melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com cofre de chaves](../general/authentication.md)
