---
title: Criar e gerir atribuições de papéis - Azure Digital Twins [ Azure Digital Twins ] Microsoft Docs
description: Aprenda sobre a criação e gestão de atribuições de papéis dentro da Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110403"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Criar e gerir atribuições de papéis em Gémeas Digitais Azure

A Azure Digital Twins utiliza o controlo de acesso baseado em papéis[(RBAC)](./security-role-based-access-control.md)para gerir o acesso aos recursos.

## <a name="role-assignments-overview"></a>Visão geral das atribuições de papéis

Cada atribuição de funções está em conformidade com a seguinte definição:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

O quadro abaixo descreve cada atributo:

| Atributo | Nome | Necessário | Tipo | Descrição |
| --- | --- | --- | --- | --- |
| roleId | Identificador de definição de papel | Sim | Cadeia | A identificação única da atribuição de funções desejada. Encontre definições de papéis e o seu identificador consultando a API do sistema ou revendo a tabela abaixo. |
| objectId | Identificador de objetos | Sim | Cadeia | Um ID de Diretório Ativo Azure, identificação principal do objeto de serviço ou nome de domínio. O que ou a quem a atribuição do papel é atribuída. A atribuição de funções deve ser formatada de acordo com o seu tipo associado. Para `DomainName` o objectIdType, o objectid deve começar com o `“@”` personagem. |
| objectIdType | Tipo de identificador de objeto | Sim | Cadeia | O tipo de identificador de objetos usado. Consulte **os ObjectIdTypes Suportados** abaixo. |
| path | Caminho espacial | Sim | Cadeia | O caminho de `Space` acesso completo ao objeto. Um exemplo é `/{Guid}/{Guid}`. Se um identificador precisar da atribuição de `"/"`funções para todo o gráfico, especifique . Este personagem designa a raiz, mas o seu uso é desencorajado. Siga sempre o Princípio do Menos Privilégio. |
| inquilinoId | Identificador de inquilino | Varia | Cadeia | Na maioria dos casos, uma identificação de inquilino do Azure Ative Directory. Não autorizados `DeviceId` `TenantId` e ObjectIdTypes. Necessário para `UserId` `ServicePrincipalId` e ObjectIdTypes. Opcional para o ObjectIdType do Nome de Domínio. |

### <a name="supported-role-definition-identifiers"></a>Identificadores de definição de funções suportados

Cada atribuição de funções associa uma definição de papel a uma entidade no seu ambiente Azure Digital Twins.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Tipos de identificador de objetos suportados

Anteriormente, o **atributo objectIdType** foi introduzido.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operações de atribuição de funções

A Azure Digital Twins suporta operações completas *create,* *READ*e *DELETE* para atribuições de papéis. As operações de *atualização* são tratadas adicionando atribuições de papéis, removendo atribuições de papéis ou modificando os nós do Gráfico de [Inteligência Espacial](./concepts-objectmodel-spatialgraph.md) a que as atribuições de papéis dão acesso.

[![Pontos finais de atribuição de funções](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

A documentação de referência da Swagger fornecida contém mais informações sobre todos os pontos finais da API disponíveis, operações de pedido e definições.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Conceda permissões ao seu diretor de serviço

Conceder permissões ao seu diretor de serviço é muitas vezes um dos primeiros passos que dará ao trabalhar com a Azure Digital Twins. Implica:

1. Iniciassem sessão na sua instância Azure através [do Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou [da PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Adquirindo a informação principal do seu serviço.
1. Atribuir o papel desejado ao seu diretor de serviço.

O ID da sua aplicação é fornecido no Diretório Ativo Azure. Para saber mais sobre configurar e fornecer um Azure Digital Twins em Diretório Ativo, leia através do [Quickstart](./quickstart-view-occupancy-dotnet.md).

Assim que tiver o ID da aplicação, execute um dos seguintes comandos. Em Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

Em Powershell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Um utilizador com a função **De Administrador** pode então atribuir a função de Administrador Espacial a um utilizador, fazendo um pedido HTTP POST autenticado ao URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Com o seguinte corpo JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Recuperar todos os papéis

[![Funções do sistema](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Para listar todas as funções disponíveis (definições de funções), faça um pedido auferido HTTP GET para:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

Um pedido bem sucedido devolverá um matriz JSON com entradas para cada função que possa ser atribuída:

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

### <a name="check-a-specific-role-assignment"></a>Verifique uma atribuição de funções específica

Para verificar uma atribuição de funções específica, faça um pedido auferido HTTP GET para:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Valor do parâmetro** | **Necessário** |  **Tipo** |  **Descrição** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Verdadeiro | Cadeia |   O objectid para o objectIdType userId. |
| YOUR_PATH | Verdadeiro | Cadeia |   O caminho escolhido para verificar o acesso. |
| YOUR_ACCESS_TYPE |  Verdadeiro | Cadeia |   *Ler,* *Criar,* *Atualizar*ou *Excluir* |
| YOUR_RESOURCE_TYPE | Verdadeiro | Cadeia |  *Dispositivo*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *Endpoint*, *KeyStore,* *Matcher,* *Ontology,* *Report*, *RoleDefinition,* *Sensor*, *SensorExtendedProperty,* *Space,* *SpaceBlobMetadata,* *SpaceExtendedProperty, SpaceExtendedProperty,* *SpaceResource,* *SpaceRoleAssignment,* *System,* * UerDefinedFunction*, *Utilizador,* *UserBlobMetadata*ou *UserExtendedProperty* |

Um pedido bem sucedido `true` devolverá uma booleana ou `false` para indicar se o tipo de acesso foi atribuído ao utilizador para o caminho e recurso dado.

### <a name="get-role-assignments-by-path"></a>Obtenha atribuições de papéis por caminho

Para obter todas as atribuições de funções para um caminho, faça um pedido autentuado HTTP GET para:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Valor | Substituir |
| --- | --- |
| YOUR_PATH | O caminho completo para o espaço |

Um pedido bem sucedido devolverá um matriz JSON com cada atribuição de funções associada ao parâmetro **de percurso** selecionado:

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

Para revogar uma permissão de um destinatário, elimine a atribuição de funções fazendo um pedido autenticado HTTP DELETE:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | A **identificação** da atribuição de funções para remover |

Um pedido DE EXCLUSÃO bem sucedido devolverá um estado de resposta 204. Verifique a remoção da atribuição de funções [verificando](#check-a-specific-role-assignment) se a atribuição de funções ainda se mantém.

### <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

Para criar uma atribuição de funções, faça um pedido de HTTP POST autenticado para o URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Verifique se o corpo da JSON está em conformidade com o seguinte esquema:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Um pedido bem sucedido devolverá um estatuto de resposta de 201 juntamente com a **identificação** da atribuição de funções recém-criada:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exemplos de configuração

Os seguintes exemplos demonstram como configurar o seu corpo JSON em vários cenários de atribuição de papéis comumente encontrados.

* **Exemplo**: Um utilizador precisa de acesso administrativo a um piso de um espaço de inquilino.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exemplo**: Uma aplicação executa cenários de teste que ridicularizam dispositivos e sensores.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exemplo**: Todos os utilizadores que fazem parte de um domínio recebem acesso lido para espaços, sensores e utilizadores. Este acesso inclui os seus objetos relacionados correspondentes.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Passos seguintes

- Para rever o controlo de acesso baseado em papéis da Azure Digital Twins, leia o controlo de [acesso de base de papel](./security-authenticating-apis.md).

- Para conhecer a autenticação da API das Gémeas Digitais Azure, leia a [autenticação API.](./security-authenticating-apis.md)
