---
title: Crie uma função personalizada de Gestor de Recursos Azure e atribua ao principal de serviço - Azure
description: Este artigo fornece orientações sobre como criar uma função personalizada de Gestor de Recursos Azure e atribuir ao principal de serviço para Live Video Analytics em IoT Edge usando Azure CLI.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 40bf0f60a718d512e02481d977b8208112ed1a55
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425736"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Crie uma função personalizada de Gestor de Recursos Azure e atribua ao diretor de serviços

Vídeo ao vivo Analítico na instância do módulo IoT Edge precisa de uma conta ativa do Azure Media Services para que funcione corretamente. A relação entre o Live Video Analytics no módulo IoT Edge e a conta Azure Media Service é estabelecida através de um conjunto de propriedades gémeas do módulo. Uma dessas propriedades gémeas é um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) que permite que a instância do módulo comunique e desencadeie as operações necessárias na conta dos Serviços de Comunicação Social. Para minimizar o uso indevido potencial e/ou exposição acidental de dados do dispositivo edge, este diretor de serviço deve ter a menor quantidade de privilégios.

Este artigo mostra-lhe os passos para criar um papel personalizado de Gestor de Recursos Azure com a Azure Cloud Shell, que depois é usado para criar um principal de serviço.

## <a name="prerequisites"></a>Pré-requisitos  

Os pré-requisitos para este artigo são os seguintes:

* Assinatura Azure com subscrição do proprietário.
* Um Diretório Ativo Azure com privilégios para criar uma app e atribuir o principal do serviço a um papel.

A forma mais fácil de verificar se a sua conta tem permissões adequadas é utilizar o portal. Veja [Permissões obrigatórias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Descrição geral  

Iremos reem pouco tempo para criar um papel personalizado e ligá-lo a um diretor de serviço na seguinte ordem:

1. Crie uma conta de Media Service, se ainda não tiver uma.
1. Criar um diretor de serviço.
1. Crie uma função personalizada de Gestor de Recursos Azure com privilégios limitados.
1. "Restringir" os privilégios principais do serviço usando o papel personalizado criado.
1. Realizar um teste simples para ver se somos capazes de restringir com sucesso o diretor de serviço.
1. Capture os parâmetros que serão utilizados nos manifestos de implantação IoT Edge.

### <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services  

Se não tiver uma conta de Media Service, use os seguintes passos para criar uma.

1. Navegue pela [Camada de Nuvem.](https://shell.azure.com/)
1. Selecione "Bash" como o seu ambiente no drop-down no lado esquerdo da janela da concha

    ![Capturas de ecrã mostram Bash selecionada a partir da janela da concha.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Desafine a subscrição do Azure como a conta predefinida utilizando o seguinte modelo de comando:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Criar um [grupo de recursos](/cli/azure/group?view=azure-cli-latest#az-group-create) e uma conta de [armazenamento.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)
1. Agora, crie uma conta Azure Media Service utilizando o seguinte modelo de comando em Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Criar um principal de serviço  

Vamos agora criar um novo diretor de serviço e ligá-lo à sua conta de Media Service.

Sem parâmetros de autenticação, a autenticação baseada em palavras-passe é utilizada com uma senha aleatória para o seu principal de serviço. Na Cloud Shell, utilize o seguinte modelo de comando:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Este comando produz uma resposta como esta:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. A saída para um principal de serviço com autenticação de senha inclui a chave de senha que neste caso é o parâmetro "AadSecret". 

    Certifique-se de copiar este valor- não pode ser recuperado. Se esquecer a palavra-passe, [reponha as credenciais principais do serviço](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials).
1. A chave appId e inquilino aparecem na saída como "AadClientId" e "AadTenantId" respectivamente. São utilizados na autenticação principal do serviço. Registem os seus valores, mas podem ser recuperados a qualquer momento com [a lista de anúncios da AZ Sp](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Criar uma definição de função personalizada  

Para criar um papel personalizado, aqui estão os passos que deve seguir:

1. Crie um ficheiro JSON de Definição de Função no seu sistema local e guarde o seguinte texto no ficheiro. 
    1. Substitua < o seu> de Subscrição Azure pelo seu ID de subscrição Azure
    1. As únicas ações permitidas para este papel são:
        * listContainerSas – ajuda os URLs de armazenamento de lista de módulos com assinaturas de acesso partilhado (SAS) para o upload e descarregamento de conteúdos de ativos.
        * Escrever ativos – ajuda o módulo a criar ou atualizar qualquer ativo
        * listEdgePolicies – lista as políticas que são aplicadas ao dispositivo edge  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Uma vez criado, executar o seguinte modelo de comando para criar a nova definição de função na subscrição:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Após a execução bem sucedida do comando, verá a seguinte saída:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Criar atribuição de funções  

Para adicionar uma atribuição de funções, você precisará do objectId do principal de serviço que deseja atribuir o papel personalizado que acabou de criar.

Use o seguinte comando em Cloud Shell para obter o objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>`pode ser recuperado a partir da saída do passo [principal do serviço Create.](#create-service-principal)

O comando acima imprimirá o objectId do principal de serviço. 

```
“objectId” : “<yourObjectId>”,
```

Use [a tarefa de função az criar](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) modelo de comando para o link o papel personalizado com o principal de serviço:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parâmetros:

|Parâmetros|Descrição| 
|---|---|
|--papel |Nome de função personalizada ou ID. No nosso caso: "Utilizador LVAEdge".|
|--assignee-object-id|ID de objeto do principal de serviço que você vai usar.|

O resultado será como:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Confirme que a atribuição de funções aconteceu

Para confirmar que o diretor de serviço está agora ligado ao papel personalizado que acabamos de criar, executar o seguinte comando:

```
az role assignment list  --assignee < objectId>
```

O resultado deve parecer:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Procure o "roleDefinitionName" e veja se o seu valor está definido para "Utilizador LVAEdge". 

Isto confirma que ligamos a função de utilizador personalizado ao principal de serviço que é utilizado para a nossa aplicação.

### <a name="test-the-service-principal-access-control"></a>Teste o controlo principal de acesso do serviço

1. Faça login usando o diretor de serviço. Para isso, precisaremos de 3 peças de informação para o Diretório Ativo Azure para nos dar o acesso adequado ao símbolo que podemos obter a partir da saída do principal passo do [serviço Create:](#create-service-principal)
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Agora, vamos tentar iniciar sessão usando o modelo de comando abaixo:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Agora, vamos ver se o login está restrito ao principal de serviço com o papel de "Utilizador LVAEdge" tentando criar um grupo de recursos para garantir que falha. Executar o seguinte comando em Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Este comando deve falhar e será como:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Passos seguintes  

Observe os seguintes valores deste artigo. Estes valores serão necessários para configurar as propriedades gémeas do Live Video Analytics no módulo IoT Edge, ver [esquema JSON twin módulo](module-twin-configuration-schema.md).

| Variável deste artigo|Nome de propriedade dupla para Live Video Analytics no IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
