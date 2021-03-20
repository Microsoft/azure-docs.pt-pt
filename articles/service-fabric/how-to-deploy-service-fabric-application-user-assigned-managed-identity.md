---
title: Implementar aplicativo com uma identidade gerida atribuída ao utilizador
description: Este artigo mostra-lhe como implementar a aplicação De Tecido de Serviço com uma identidade gerida atribuída pelo utilizador
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260093"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Implementar aplicação de tecido de serviço com uma identidade gerida User-Assigned

Para implementar uma aplicação de Tecido de Serviço com identidade gerida, a aplicação precisa de ser implementada através do Azure Resource Manager, tipicamente com um modelo de Gestor de Recursos Azure. Para obter mais informações sobre como implementar a aplicação de Tecido de Serviço através do Azure Resource Manager, consulte [Gerir aplicações e serviços como recursos do Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> As aplicações que não são implantadas como um recurso Azure **não podem** ter Identidades Geridas. 
>
> A implementação da aplicação de tecido de serviço com identidade gerida é suportada com a versão `"2019-06-01-preview"` API. Também pode utilizar a mesma versão API para tipo de aplicação, versão tipo de aplicação e recursos de serviço.
>

## <a name="user-assigned-identity"></a>Identidade User-Assigned

Para ativar a aplicação com User-Assigned identidade, adicione primeiro a propriedade **de identidade** ao recurso de aplicação com o **utilizador tipoAssigned** e as identidades atribuídas pelo utilizador referenciado. Em seguida, adicione uma secção **de identificação gerida** dentro da secção de **propriedades** para o recurso de **aplicação** que contém uma lista de nome amigável para mapeamento principal para cada uma das identidades atribuídas pelo utilizador. Para obter mais informações sobre identidades atribuídas ao utilizador consulte [Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)

### <a name="application-template"></a>Modelo de aplicação

Para ativar a aplicação com identidade atribuída ao utilizador, adicione primeiro a propriedade **de identidade** ao recurso de aplicação com o **utilizador tipoAssigned** e as identidades atribuídas pelo utilizador referenciado, em seguida, adicione um objeto **de identificação gerida** dentro da secção de **propriedades** que contém uma lista de nome amigável para mapeamento principal para cada uma das identidades atribuídas pelo utilizador.

```json
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
```

No exemplo acima, o nome de recurso da identidade atribuída ao utilizador está a ser usado como o nome amigável da identidade gerida para a aplicação. Os exemplos a seguir assumem que o nome verdadeiro amigável é "AdminUser".

### <a name="application-package"></a>Pacote de candidaturas

1. Para cada identidade definida na `managedIdentities` secção no modelo Azure Resource Manager, adicione uma `<ManagedIdentity>` etiqueta no manifesto de aplicação na secção **Principais.** O `Name` atributo precisa de corresponder à propriedade definida na `name` `managedIdentities` secção.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Na secção **ServiceManifestImport,** adicione uma **IdentityBindingPolicy** para o serviço que utiliza a Identidade Gerida. Esta política mapeia a `AdminUser` identidade para um nome de identidade específico de serviço que precisa de ser adicionado ao manifesto de serviço mais tarde.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Atualizar o manifesto de serviço para adicionar uma **Entidade ManagedId** dentro da secção **Recursos** com o nome correspondente `ServiceIdentityRef` ao manifesto da `IdentityBindingPolicy` aplicação:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Passos seguintes

* [Como utilizar identidade gerida no código de aplicação do tecido de serviço](how-to-managed-identity-service-fabric-app-code.md)
* [Como conceder acesso à aplicação Service Fabric a outros recursos da Azure](how-to-grant-access-other-resources.md)
