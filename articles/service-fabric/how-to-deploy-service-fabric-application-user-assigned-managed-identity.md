---
title: Service Fabric do Azure – implantar aplicativo com uma identidade gerenciada atribuída pelo usuário | Microsoft Docs
description: Este artigo mostra como implantar Service Fabric aplicativo com uma identidade gerenciada atribuída pelo usuário
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: 0cc1e51a4d5f9ad54866066a4247e1588da381a6
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037496"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Implantar Service Fabric aplicativo com uma identidade gerenciada atribuída pelo usuário (versão prévia)

Para implantar um aplicativo Service Fabric com identidade gerenciada, o aplicativo precisa ser implantado por meio de Azure Resource Manager, normalmente com um modelo de Azure Resource Manager. Para obter mais informações sobre como implantar Service Fabric aplicativo por meio de Azure Resource Manager, consulte [gerenciar aplicativos e serviços como Azure Resource Manager recursos](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Os aplicativos que não são implantados como um recurso do Azure **não podem** ter identidades gerenciadas. 
>
> Service Fabric implantação de aplicativo com identidade gerenciada tem suporte com `"2019-06-01-preview"`a versão de API. Você também pode usar a mesma versão de API para tipo de aplicativo, versão de tipo de aplicativo e recursos de serviço.
>

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Para habilitar o aplicativo com identidade atribuída pelo usuário, primeiro adicione a propriedade **Identity** ao recurso do aplicativo com o tipo **userassigned** e as identidades atribuídas ao usuário referenciadas. Em seguida, adicione uma seção **managedIdentities** dentro da seção **Propriedades** para o recurso de **aplicativo** que contém uma lista de nomes amigáveis para o mapeamento de PrincipalId para cada uma das identidades atribuídas pelo usuário. Para obter mais informações sobre identidades atribuídas ao usuário [, consulte criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Modelo de aplicação

Para habilitar o aplicativo com identidade atribuída pelo usuário, primeiro adicione a propriedade **Identity** ao recurso do aplicativo com o tipo **userassigned** e as identidades atribuídas ao usuário referenciadas e adicione um objeto **managedIdentities** dentro doa seção de propriedades que contém uma lista de nomes amigáveis para o mapeamento de PrincipalId para cada uma das identidades atribuídas pelo usuário.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

No exemplo acima, o nome do recurso da identidade atribuída ao usuário está sendo usado como o nome amigável da identidade gerenciada para o aplicativo. Os exemplos a seguir pressupõem que o nome amigável real é "AdminUser".

### <a name="application-package"></a>Pacote de aplicação

1. Para cada identidade definida na `managedIdentities` seção no modelo de Azure Resource Manager, adicione uma `<ManagedIdentity>` marca no manifesto do aplicativo na seção **principais** . O `Name` atributo precisa `name` corresponder à propriedade definida na `managedIdentities` seção.

    **ApplicationManifest. xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Na seção **ServiceManifestImport** , adicione um **IdentityBindingPolicy** para o serviço que usa a identidade gerenciada. Essa política mapeia a `AdminUser` identidade para um nome de identidade específico do serviço que precisa ser adicionado ao manifesto do serviço posteriormente.

    **ApplicationManifest. xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Atualize o manifesto do serviço para adicionar um **ManagedIdentity** dentro da seção de **recursos** com o nome `ServiceIdentityRef` correspondente `IdentityBindingPolicy` ao no do manifesto do aplicativo:

    **Manifesto. xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Como usar a identidade gerenciada no código do aplicativo Service Fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Como conceder acesso Service Fabric aplicativo a outros recursos do Azure](how-to-grant-access-other-resources.md)
