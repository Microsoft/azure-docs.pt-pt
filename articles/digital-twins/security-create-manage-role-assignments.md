---
title: Criar e gerenciar atribuições de função – gêmeos digital do Azure | Microsoft Docs
description: Saiba mais sobre como criar e gerenciar atribuições de função no Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: 45ce22f208ee31b7202705eb4e42c38bedf09a8b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013956"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Criar e gerenciar atribuições de função no gêmeos digital do Azure

O Azure digital gêmeos usa o[RBAC](./security-role-based-access-control.md)(controle de acesso baseado em função) para gerenciar o acesso aos recursos.

## <a name="role-assignments-overview"></a>Visão geral das atribuições de função

Cada atribuição de função está de acordo com a seguinte definição:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

A tabela a seguir descreve cada atributo:

| Atributo | Nome | Necessário | Tipo | Descrição |
| --- | --- | --- | --- | --- |
| roleId | Identificador de definição de função | Sim | Cadeia | A ID exclusiva da atribuição de função desejada. Encontre definições de função e seu identificador consultando a API do sistema ou revisando a tabela abaixo. |
| objectId | Identificador de objeto | Sim | Cadeia | Uma ID de Azure Active Directory, ID de objeto da entidade de serviço ou nome de domínio. A que ou a quem a atribuição de função está atribuída. A atribuição de função deve ser formatada de acordo com seu tipo associado. Para a `DomainName` objectIdtype, objectId deve começar com o caractere de `“@”`. |
| objectIdType | Tipo de identificador de objeto | Sim | Cadeia | O tipo de identificador de objeto usado. Consulte **ObjectIdTypes com suporte** abaixo. |
| path | Caminho do espaço | Sim | Cadeia | O caminho de acesso completo para o objeto `Space`. Um exemplo é `/{Guid}/{Guid}`. Se um identificador precisar da atribuição de função para o grafo inteiro, especifique `"/"`. Esse caractere designa a raiz, mas seu uso não é recomendado. Sempre siga o princípio de privilégios mínimos. |
| tenantId | Identificador do locatário | Varia | Cadeia | Na maioria dos casos, uma ID de locatário Azure Active Directory. Não permitido para `DeviceId` e `TenantId` ObjectIdTypes. Necessário para `UserId` e `ServicePrincipalId` ObjectIdTypes. Opcional para o ObjectIdid DomainName. |

### <a name="supported-role-definition-identifiers"></a>Identificadores de definição de função com suporte

Cada atribuição de função associa uma definição de função a uma entidade em seu ambiente de gêmeos digital do Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Tipos de identificador de objeto com suporte

Anteriormente, o atributo **objectidtype** foi introduzido.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operações de atribuição de função

O Azure digital gêmeos dá suporte a operações de *criação*, *leitura*e *exclusão* completas para atribuições de função. As operações de *atualização* são tratadas adicionando atribuições de função, removendo atribuições de função ou modificando os nós de [grafo de inteligência espacial](./concepts-objectmodel-spatialgraph.md) aos quais as atribuições de função concedem acesso.

[pontos de extremidade de atribuição de função ![](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

A documentação de referência do Swagger fornecida contém mais informações sobre todos os pontos de extremidade de API, operações de solicitação e definições disponíveis.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Conceder permissões para a entidade de serviço

A concessão de permissões para sua entidade de serviço geralmente é uma das primeiras etapas que você seguirá ao trabalhar com o Azure digital gêmeos. Isso envolve:

1. Fazer logon em sua instância do Azure por meio do [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou do [PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Adquirindo as informações da entidade de serviço.
1. Atribuindo a função desejada à entidade de serviço.

A ID do aplicativo é fornecida a você no Azure Active Directory. Para saber mais sobre como configurar e provisionar um gêmeos digital do Azure no Active Directory, leia o guia de [início rápido](./quickstart-view-occupancy-dotnet.md).

Depois de ter a ID do aplicativo, execute um dos comandos a seguir. Em CLI do Azure:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

No PowerShell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Um usuário com a função de **administrador** pode atribuir a função de administrador de espaço a um usuário fazendo uma solicitação HTTP post autenticada para a URL:

```plaintext
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

### <a name="retrieve-all-roles"></a>Recuperar todas as funções

[![funções do sistema](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Para listar todas as funções disponíveis (definições de função), faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Uma solicitação bem-sucedida retornará uma matriz JSON com entradas para cada função que pode ser atribuída:

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

### <a name="check-a-specific-role-assignment"></a>Verificar uma atribuição de função específica

Para verificar uma atribuição de função específica, faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Valor do parâmetro** | **Necessário** |  **Tipo** |  **Descrição** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Verdadeiro | Cadeia |   O objectId do objectIdtype de UserId. |
| YOUR_PATH | Verdadeiro | Cadeia |   O caminho escolhido para verificar o acesso. |
| YOUR_ACCESS_TYPE |  Verdadeiro | Cadeia |   *Ler*, *criar*, *Atualizar*ou *excluir* |
| YOUR_RESOURCE_TYPE | Verdadeiro | Cadeia |  *Dispositivo*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *Extended*, *ponto de extremidade*, *keystore*, *correspondência*, *ontologia*, *relatório*, *RoleDefinition*, *sensor*, *SensorExtendedProperty*, *espaço*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *sistema*,  *UerDefinedFunction*, *User*, *UserBlobMetadata*ou *extendeproperty* |

Uma solicitação bem-sucedida retornará um `true` booliano ou `false` para indicar se o tipo de acesso foi atribuído ao usuário para o caminho e o recurso especificados.

### <a name="get-role-assignments-by-path"></a>Obter atribuições de função por caminho

Para obter todas as atribuições de função para um caminho, faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Valor | Substituir |
| --- | --- |
| YOUR_PATH | O caminho completo para o espaço |

Uma solicitação bem-sucedida retornará uma matriz JSON com cada atribuição de função associada ao parâmetro de **caminho** selecionado:

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

Para revogar uma permissão de um destinatário, exclua a atribuição de função fazendo uma solicitação HTTP DELETE autenticada:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | A **ID** da atribuição de função a ser removida |

Uma solicitação de exclusão bem-sucedida retornará um status de resposta 204. Verifique a remoção da atribuição de função [verificando](#check-a-specific-role-assignment) se a atribuição de função ainda é mantida.

### <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Para criar uma atribuição de função, faça uma solicitação HTTP POST autenticada para a URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Verifique se o corpo JSON está de acordo com o esquema a seguir:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Uma solicitação bem-sucedida retornará um status de resposta 201 junto com a **ID** da atribuição de função recém-criada:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exemplos de configuração

Os exemplos a seguir demonstram como configurar seu corpo JSON em vários cenários de atribuição de função comumente encontrados.

* **Exemplo**: um usuário precisa de acesso administrativo a um andar de um espaço de locatário.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exemplo**: um aplicativo executa dispositivos de simulação de cenários de teste e sensores.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exemplo**: todos os usuários que fazem parte de um domínio recebem acesso de leitura para espaços, sensores e usuários. Esse acesso inclui seus objetos relacionados correspondentes.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Passos seguintes

- Para examinar o controle de acesso baseado em função do gêmeos digital do Azure, leia [função-base-acesso-Control](./security-authenticating-apis.md).

- Para saber mais sobre a autenticação da API do Azure digital gêmeos, leia [autenticação de API](./security-authenticating-apis.md).
