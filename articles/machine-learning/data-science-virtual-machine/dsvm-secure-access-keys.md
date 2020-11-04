---
title: Armazenar credenciais de acesso de forma segura
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como armazenar de forma segura credenciais de acesso na Máquina Virtual de Ciência de Dados. Você vai aprender a usar identidades de serviço geridas e Azure Key Vault para armazenar credenciais de acesso.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: d5604e42c2c27463e10c136ccd18c3c21846fc5a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309148"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Armazenar credenciais de acesso seguramente numa Máquina Virtual Azure Data Science

É comum que o código em aplicações em nuvem contenha credenciais para autenticação em serviços na nuvem. Como gerir e proteger estas credenciais é um desafio bem conhecido na construção de aplicações em nuvem. Idealmente, as credenciais nunca devem aparecer em postos de trabalho do desenvolvedor ou ser check-in para o controlo de fontes.

As identidades geridas para a funcionalidade [de recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md) simplificam a resolução deste problema, dando aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporta a autenticação Azure AD sem ter que ter nenhumas credenciais no seu código.

Uma forma de garantir credenciais é utilizar o Instalador do Windows (MSI) em combinação com [o Azure Key Vault](../../key-vault/index.yml), um serviço gerido azure para armazenar segredos e chaves criptográficas de forma segura. Você pode aceder a um cofre chave usando a identidade gerida e, em seguida, recuperar os segredos autorizados e chaves criptográficas do cofre da chave.

A documentação sobre identidades geridas para recursos Azure e Key Vault compreende um recurso abrangente para informações aprofundadas sobre estes serviços. O resto deste artigo percorre o uso básico de MSI e Key Vault na Data Science Virtual Machine (DSVM) para aceder aos recursos do Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Criar uma identidade gerida no DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Atribuir permissões de acesso ao Cofre chave a um diretor de VM

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Aceda a um segredo no cofre da DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Chaves de armazenamento de acesso do DSVM

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Aceda ao cofre de chaves de Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Aceda ao cofre de chaves a partir de Azure CLI

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```