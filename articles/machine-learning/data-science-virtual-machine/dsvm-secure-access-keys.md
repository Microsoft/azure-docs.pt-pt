---
title: Store credenciais de acesso na máquina de Virtual de ciência de dados de forma segura - Azure | Documentos da Microsoft
description: Saiba como armazenar em segurança as credenciais de acesso na máquina de Virtual de ciência de dados. Ficará a saber como utilizar identidades de serviço geridas e o Azure Key Vault para armazenar credenciais de acesso.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise geoespacial, o processo de ciência de dados de equipa, aprendizagem aprofundada
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 79dba586a5f7102d0012c381593551a951f1b38e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502354"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Store acesso credenciais na máquina de Virtual de ciência de dados de forma segura

Um desafio comum em criar aplicações na cloud é a forma de gerir as credenciais que têm de estar no seu código para autenticar a serviços em nuvem. Manter essas credenciais protegidas é uma tarefa importante. O ideal é que eles nunca são apresentados nas estações de trabalho de programador ou obterem check-in ao controlo de origem. 

[Gerido identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) torna a resolver esse problema mais simples, fornecendo Azure dos serviços de uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter de quaisquer credenciais no seu código. 

Uma forma de proteger credenciais é utilizar MSI em combinação com [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), um serviço do Azure para armazenar as chaves criptográficas e segredos em segurança gerida. Pode aceder a um cofre de chaves ao utilizar a identidade gerida e recuperar o autorizados segredos e chaves criptográficas do Cofre de chaves. 

As identidades geridas para recursos do Azure e a documentação do Key Vault é um recurso abrangente para obter informações aprofundadas sobre esses serviços. O restante deste artigo descreve a utilização básica de MSI e o Cofre de chaves no Máquina Virtual de ciência de dados (DSVM) para aceder aos recursos do Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Criar uma identidade gerida no DSVM 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permission-to-a-vm-principal"></a>Atribuir a permissão de acesso do Cofre de chaves para uma entidade de segurança de VM
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI. 

# Assign only get and set permission but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Um segredo no Cofre de chaves de acesso da DSVM

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Chaves de acesso de armazenamento da DSVM

```
# Prerequisite: You have granted your VM's MSI access to use storage account access keys based on instructions from the article at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>Acesso ao Cofre de chaves do Python

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
"SQLPasswd",       # The name of your secret that already exists in the key vault.
""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Aceder ao Cofre de chaves a partir da CLI do Azure

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. Here are commands to access the key vault from Azure CLI without having to log in to an Azure account. 
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permission, like accessing storage account keys, reading specific secrets, and writing new secrets, is provided to the MSI. 

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
