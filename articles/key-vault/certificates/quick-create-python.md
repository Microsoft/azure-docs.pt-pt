---
title: Quickstart - Biblioteca de clientes Azure Key Vault para Python
description: Aprenda a criar, recuperar e apagar certificados de um cofre chave Azure usando a biblioteca de clientes Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 8bac2af112bbecd90ce3ba3aa63475071d6edb26
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901225"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Quickstart: Biblioteca de clientes Azure Key Vault para Python

Começa com a biblioteca de clientes azure Key Vault para python. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca de clientes Key Vault para Python para:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Crie e importe chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e o despedimento global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize HSMs validados de nível 2 fips 140-2.

[Documentação de](/python/api/overview/azure/key-vault?view=azure-python) | referência API Pacote de[código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [(Índice de Pacote Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Python 2.7, 3.5.3, ou mais tarde
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este quickstart assume que está a executar [o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) numa janela terminal linux.

## <a name="setting-up"></a>Configuração

### <a name="install-the-package"></a>Instale o pacote

A partir da janela da consola, instale a biblioteca de certificados Azure Key Vault para Python.

```console
pip install azure-keyvault-certificates
```

Para este arranque rápido, terá de instalar também o pacote azure.identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre chave

Este quickstart usa um cofre chave Azure pré-criado. Pode criar um cofre chave seguindo os passos no [quickstart Azure CLI,](quick-create-cli.md) [Azure PowerShell quickstart](quick-create-powershell.md)ou [portal Azure quickstart](quick-create-portal.md). Em alternativa, pode executar os comandos Azure CLI abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <seu> de nome único com o seu cofre único com o nome do seu cofre-chave nos seguintes exemplos.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A forma mais simples de autenticar uma aplicação .NET baseada na nuvem é com uma identidade gerida; ver Utilize um Serviço de [Aplicações gerido identidade para aceder ao Cofre de Chaves Azure](../general/managed-identity.md) para obter mais detalhes.

No entanto, por uma questão de simplicidade, este quickstart cria uma aplicação de ambiente de trabalho, que requer a utilização de um diretor de serviço e de uma política de controlo de acesso. O seu princípio de serviço requer um nome único no formato "http://<my-unique-service-principle-name>".

Crie um princípio de serviço utilizando o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principle-name>" --sdk-auth
```

Esta operação devolverá uma série de pares chave/valor. 

```console
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

Tome nota do clienteId e clienteSecret, pois iremos usá-los no [Passo Variável Ambiental set](#set-environmental-variables) abaixo.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao principal serviço acesso ao seu cofre chave

Crie uma política de acesso para o seu cofre chave que concede permissão ao seu diretor de serviço, passando o clienteId para o comando [de definição de teclado az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Dê ao diretor de serviço obter, listar e criar permissões para certificados.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Definir variáveis ambientais

O método DefaultAzureCredential na nossa aplicação baseia-se em três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. Detete estas variáveis para os valores clienteId, clientSecret e tenantId `export VARNAME=VALUE` que observou no passo principal do serviço [Create](#create-a-service-principal) usando o formato. (Este método apenas define as variáveis para a sua concha atual e os processos criados `/etc/environment ` a partir da concha; para adicionar permanentemente estas variáveis ao seu ambiente, editar o seu ficheiro.) 

Você também precisa guardar o seu nome chave `KEY_VAULT_NAME`do cofre como uma variável ambiental chamada .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para python permite-lhe gerir chaves e bens relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, criar um certificado, recuperar um certificado e apagar um certificado.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Autenticar o seu cofre chave e criar um cliente chave vault depende das variáveis ambientais nas [variáveis ambientais definidas](#set-environmental-variables) acima. O nome do seu cofre chave é expandido para o cofre de chaves URI, no formato "https://<your-key-vault-name>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Guardar um certificado

Agora que o seu pedido é autenticado, pode colocar um certificado auto-assinado no seu cofre de chaves. 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

Pode verificar se o certificado foi definido com o comando de certificado de [cofre az keyvault:](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Agora pode recuperar o certificado previamente criado

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

O seu certificado `retrieved_certificate`está agora guardado como .

### <a name="delete-a-certificate"></a>Eliminar um certificado

Finalmente, vamos apagar o certificado do seu cofre chave

```python
client.delete_certificate(certificateName)
```

Pode verificar se o certificado se foi com o comando de certificado de [cofre az keyvault:](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
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

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um cofre chave, guardou um certificado e recuperou o certificado. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Consulte o guia do desenvolvedor do Cofre de [Chaves Azure](../general/developers-guide.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)
