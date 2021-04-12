---
title: 'Quickstart: Criar uma conta de visão usando Python'
description: Crie uma conta Azure Purview usando Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387511"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Quickstart: Criar uma conta de visão usando Python

Neste arranque rápido, você cria uma conta Purview usando Python. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* O seu próprio [inquilino do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A sua conta deve ter permissão para criar recursos na subscrição

* Se tiver **a Azure Policy** a bloquear todas as aplicações da criação de conta de **Armazenamento** e espaço **de nomes EventHub,** tem de abrir exceções de política utilizando a etiqueta, que pode ser inserida durante o processo de criação de uma conta Purview. A razão principal é que para cada Conta Desembaraço criada, precisa de criar um Grupo de Recursos gerido e dentro deste grupo de recursos, uma conta de Armazenamento e um espaço de nomes EventHub. Para mais informações consulte o [Portal do Catálogo](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Instalar o pacote do Python

1. Abra um terminal ou uma linha de comandos com privilégios de administrador. 
2. Em primeiro lugar, instale o pacote do Python para recursos de gestão do Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Para instalar o pacote Python para o Purview, execute o seguinte comando:

    ```python
    pip install azure-mgmt-purview
    ```

    O [Python SDK para a Purga](https://github.com/Azure/azure-sdk-for-python) suporta python 2.7, 3.3, 3.4, 3.5, 3.6 e 3.7.

4. Para instalar o pacote Python para autenticação de identidade Azure, executar o seguinte comando:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > O pacote "identidade azul" pode ter conflitos com "azure-cli" em algumas dependências comuns. Se encontrar algum problema de autenticação, remova "azure-cli" e suas dependências, ou utilize uma máquina limpa sem instalar o pacote "azure-cli" para fazê-lo funcionar.
    
## <a name="create-a-purview-client"></a>Criar um cliente de competência

1. Crie um ficheiro chamado **purview.py**. Adicione as seguintes instruções para adicionar referências aos espaços de nomes.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Adicione o seguinte código ao método **Principal** que cria uma instância de classe PurviewManagementClient. Utiliza este objeto para criar uma conta de competência, eliminar conta de competência, verificar disponibilidade de nome e outras operações do fornecedor de recursos.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Criar uma conta de competência

Adicione o seguinte código ao método **Principal** que cria uma **conta de competência**. Se o grupo de recursos já existir, comente a primeira instrução `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Agora, adicione a instrução seguinte para invocar o método **main** quando o programa é executado:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Eis o código de Python completo:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Executar o código

Crie e inicie a aplicação e, em seguida, verifique a execução de pipeline.

A consola imprime o progresso da criação da fábrica de dados, o serviço ligado, os conjuntos de dados, o pipeline e a execução de pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados lidos/escritos. Em seguida, utilize ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados para "outputBlobPath" a partir de "inputBlobPath", conforme especificou nas variáveis.

Segue-se o resultado do exemplo:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Verificar a saída

Aceda à página de **contas do 'Purview'** no portal Azure e verifique a conta criada através do código acima. 

## <a name="delete-purview-account"></a>Eliminar Conta De Purga

Para eliminar a conta de competência, adicione o seguinte código ao programa:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Passos seguintes

O código deste tutorial cria uma conta de competência e elimina uma conta de competência. Agora pode baixar o Python SDK e aprender sobre outras ações do fornecedor de recursos que pode realizar para uma conta Purview.

Avance para o próximo artigo para saber como permitir que os utilizadores acedam à sua Conta Azure Purview. 

> [!div class="nextstepaction"]
> [Adicione utilizadores à sua Conta Azure Purview](catalog-permissions.md)
