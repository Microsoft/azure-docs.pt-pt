---
title: Criar e gerir atribuições de funções no duplos Digital do Azure | Documentos da Microsoft
description: Criar e gerir atribuições de funções no duplos Digital do Azure.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162141"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Criar e gerir atribuições de funções no duplos Digital do Azure

Os gémeos Digital do Azure utiliza o controlo de acesso baseado em funções ([RBAC](./security-role-based-access-control.md)) para gerir o acesso aos recursos.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Descrição geral de atribuições de função

Cada atribuição de função está em conformidade com a seguinte definição:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

A tabela abaixo descreve cada atributo:

| Atributo | Name | Necessário | Tipo | Descrição |
| --- | --- | --- | --- | --- |
| roleId | Identificador de definição de função | Sim | String | O ID exclusivo da atribuição de função desejada. Encontre as definições de função e seu identificador consultando a API de sistema ou consultar a tabela abaixo. |
| objectId | Identificador de objeto | Sim | String | Um ID de diretório do Azure Active Directory, o ID de objeto do principal de serviço ou o nome de domínio. O que, ou quem a atribuição de função é atribuída a. A atribuição de função têm de ser formatada vzhledem KE svému typu associado. Para o `DomainName` objectIdType, objectId tem de começar com o `“@”` caráter. |
| objectIdType | Tipo de identificador de objeto | Sim | String | O tipo de identificador de objeto utilizado. Ver **suportado ObjectIdTypes** abaixo. |
| path | Caminho de espaço | Sim | String | O caminho de acesso total para o `Space` objeto. Um exemplo é `/{Guid}/{Guid}`. Se precisar de um identificador a atribuição de função para todo o gráfico, especifique `"/"`. Este caráter designa a raiz, mas a sua utilização não é recomendada. Seguem sempre o princípio do menor privilégio. |
| tenantId | Identificador do inquilino | Varia | String | Na maioria dos casos, um inquilino do Azure Active Directory ID. Não são permitidas para `DeviceId` e `TenantId` ObjectIdTypes. Necessário para `UserId` e `ServicePrincipalId` ObjectIdTypes. Opcional para o DomainName ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Identificadores da definição de função suportadas

Cada atribuição de função associa uma definição de função com uma entidade no seu ambiente de duplos Digital do Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Tipos de identificador de objeto suportados

Anteriormente, o **objectIdType** atributo foi introduzido.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operações de atribuição de função

Duplos Digital do Azure oferece suporte completo *CREATE*, *ler*, e *eliminar* operações para atribuições de funções. *ATUALIZAÇÃO* operações são processadas as atribuições de funções a adicionar, remover atribuições de funções ou modificando o [geográficos de inteligência de gráfico](./concepts-objectmodel-spatialgraph.md) nós que atribuições de funções fornecem acesso a.

![Pontos finais de atribuição de função][1]

A documentação de referência do Swagger fornecida contém mais informações sobre todas as disponíveis API pontos de extremidade, operações de pedidos e definições.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Conceder permissões a seu principal de serviço

Conceder permissões para a sua principal de serviço é, muitas vezes, uma das primeiras etapas que toma quando se trabalha com duplos Digital do Azure. Implícito:

1. Iniciar sessão na sua instância do Azure através do PowerShell.
1. A adquirir as suas informações do principal de serviço.
1. Atribuir a função desejada para o seu principal de serviço.

ID da sua aplicação é fornecido para si no Azure Active Directory. Para saber mais sobre como configurar e aprovisionar um duplos Digital do Azure no Active Directory, leia os [guia de introdução](./quickstart-view-occupancy-dotnet.md).

Assim que tiver o ID da aplicação, execute os seguintes comandos do PowerShell:

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

Um utilizador com o **administrador** função, em seguida, pode atribuir a função de administrador de espaço para um utilizador ao fazer um pedido de HTTP POST autenticado para o URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Com o corpo JSON seguinte:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Obter todas as funções

![Funções do sistema][2]

Para listar todas as funções disponíveis (definições de função), fazer um pedido de HTTP GET autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Um pedido com êxito irá devolver uma matriz JSON com entradas para cada função que podem ser atribuídos:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Verificar uma atribuição de função específica

Para verificar uma atribuição de função específica, fazer um pedido HTTP GET autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Valor do parâmetro** | **Necessário** |  **Tipo** |  **Descrição** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Verdadeiro | String |   O objectId para objectIdType o ID de utilizador. |
| YOUR_PATH | Verdadeiro | String |   O caminho escolhido para verificar o acesso. |
| YOUR_ACCESS_TYPE |  Verdadeiro | String |   O tipo de acesso para procurar. |
| YOUR_RESOURCE_TYPE | Verdadeiro | String |  O recurso para verificar. |

Um pedido com êxito irá devolver um valor booleano `true` ou `false` para indicar se o tipo de acesso foi atribuído ao utilizador para o caminho especificado e o recurso.

### <a name="get-role-assignments-by-path"></a>Obter atribuições de funções através do caminho

Para obter todas as atribuições de funções para um caminho, fazer um pedido de HTTP GET autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Value | Substituir |
| --- | --- |
| YOUR_PATH | O caminho completo para o espaço |

Um pedido com êxito irá devolver uma matriz JSON com cada atribuição de função associada selecionado **caminho** parâmetro:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Revogar uma permissão

Para revogar um permissões de um destinatário, elimine a atribuição de função, fazendo uma solicitação HTTP DELETE autenticada:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | O **id** de remover a atribuição de função |

Um pedido de eliminação com êxito irá devolver um Estado de 204 resposta. Certifique-se a remoção da atribuição de função por [verificação](#check) se a atribuição de função ainda se aplica.

### <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Para criar uma atribuição de função, efetue um pedido de HTTP POST autenticado para o URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Certifique-se de que o corpo JSON está em conformidade com o esquema seguinte:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Um pedido com êxito irá devolver um Estado de 201 resposta juntamente com o **id** da atribuição de função recentemente criada:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exemplos de configuração

Os exemplos seguintes demonstram como configurar o seu corpo JSON em vários cenários de atribuição de função comumente encontrados.

* **Exemplo**: Um utilizador tem acesso administrativo a um andar de um espaço de inquilino.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exemplo**: Dispositivos e sensores de simulação de cenários de teste de execução de um aplicativo.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exemplo**: Todos os utilizadores que fazem parte de um domínio recebem acesso de leitura para usuários, sensores e espaços. Este acesso inclui seus objetos relacionados correspondentes.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Passos Seguintes

- Para rever duplos Digital do Azure-com base--controlo de acesso funções, leia [função-base-access-control](./security-authenticating-apis.md).

- Para saber mais sobre a autenticação de API de duplos Digital do Azure, leia [autenticação de API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
