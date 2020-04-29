---
title: Implementar app com uma identidade gerida atribuída pelo utilizador
description: Este artigo mostra-lhe como implementar aplicação Service Fabric com uma identidade gerida atribuída pelo utilizador
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415635"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Implementar aplicação de tecido de serviço com uma identidade gerida atribuída pelo utilizador

Para implementar uma aplicação de Tecido de Serviço com identidade gerida, a aplicação precisa de ser implementada através do Gestor de Recursos Azure, tipicamente com um modelo de Gestor de Recursos Azure. Para obter mais informações sobre como implementar a aplicação service Fabric através do Gestor de Recursos Azure, consulte [gerir aplicações e serviços como recursos do Gestor de Recursos Azure.](service-fabric-application-arm-resource.md)

> [!NOTE] 
> 
> Aplicações que não são implementadas como recurso Azure **não podem** ter Identidades Geridas. 
>
> A implementação da aplicação Service Fabric com `"2019-06-01-preview"`identidade gerida é suportada com versão API . Também pode utilizar a mesma versão API para o tipo de aplicação, versão tipo de aplicação e recursos de serviço.
>

## <a name="user-assigned-identity"></a>Identidade atribuída ao utilizador

Para ativar a aplicação com identidade Atribuída pelo Utilizador, adicione primeiro a propriedade **de identidade** ao recurso da aplicação com o utilizador do tipo **Assigned** e as identidades atribuídas ao utilizador referenciadas. Em seguida, adicione uma secção de **identidades geridas** dentro da secção **de propriedades** para o recurso de **aplicação** que contém uma lista de nome amigável para mapeamento principalid para cada uma das identidades atribuídas pelo utilizador. Para obter mais informações sobre identidades atribuídas ao utilizador consulte [Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Modelo de aplicação

Para ativar a aplicação com identidade atribuída ao Utilizador, primeiro adicione propriedade **de identidade** ao recurso de aplicação com o utilizador **do tipoAssigned** e as identidades atribuídas ao utilizador referenciado, em seguida, adicione um objeto **de identidades geridas** dentro da secção **de propriedades** que contém uma lista de nome amigável para mapeamento principalId para cada um dos utilizadores designados identidades.

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

No exemplo acima do nome de recurso da identidade atribuída ao utilizador está a ser usado como o nome amigável da identidade gerida para a aplicação. Os exemplos que se seguem assumem que o nome verdadeiro é "AdminUser".

### <a name="application-package"></a>Pacote de candidatura

1. Para cada identidade `managedIdentities` definida na secção do modelo do `<ManagedIdentity>` Gestor de Recursos Azure, adicione uma etiqueta no manifesto de aplicação sob a secção **principais.** O `Name` atributo precisa `name` de corresponder `managedIdentities` à propriedade definida na secção.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Na secção **ServiceManifestImport,** adicione uma Política de **Ligação de Identidade** para o serviço que utiliza a Identidade Gerida. Esta política `AdminUser` mapeia a identidade para um nome de identidade específico do serviço que precisa de ser adicionado ao manifesto de serviço mais tarde.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Atualize o manifesto de serviço para adicionar uma **Identidade Gerida** `IdentityBindingPolicy` dentro da secção **Recursos** com o nome correspondente `ServiceIdentityRef` ao do manifesto de aplicação:

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

* [Como utilizar identidade gerida no código de aplicação de tecido de serviço](how-to-managed-identity-service-fabric-app-code.md)
* [Como conceder acesso à aplicação de serviço fabric a outros recursos Azure](how-to-grant-access-other-resources.md)
