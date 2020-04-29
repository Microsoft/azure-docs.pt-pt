---
title: Implementar uma aplicação service Fabric com MI atribuído ao sistema
description: Este artigo mostra-lhe como atribuir uma identidade gerida atribuída pelo sistema a uma aplicação Azure Service Fabric
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415658"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Implementar aplicação de tecido de serviço com identidade gerida atribuída pelo sistema

Para aceder à funcionalidade de identidade gerida para aplicações Azure Service Fabric, tem primeiro de ativar o Serviço de Fichas de Identidade Gerida no cluster. Este serviço é responsável pela autenticação de aplicações de Tecido de Serviço utilizando as suas identidades geridas e pela obtenção de fichas de acesso em seu nome. Uma vez ativado o serviço, pode vê-lo no Service Fabric Explorer sob a secção **Sistema** no painel esquerdo, funcionando sob o nome **de tecido:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE] 
> A implementação de aplicações de Tecido de Serviço com `"2019-06-01-preview"`identidades geridas são suportadas a partir da versão API . Também pode utilizar a mesma versão API para o tipo de aplicação, versão tipo de aplicação e recursos de serviço. O tempo mínimo de execução do Tecido de Serviço suportado é de 6,5 CU2. Em additoina, o ambiente de construção/pacote também deve ter o SF .Net SDK em CU2 ou superior

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

### <a name="application-template"></a>Modelo de aplicação

Para permitir a aplicação com uma identidade gerida atribuída pelo sistema, adicione a propriedade **de identidade** ao recurso de aplicação, com sistema de tipo **Astas como** indicado no exemplo abaixo:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Este imóvel declara (ao Gestor de Recursos Azure, e aos Fornecedores de Recursos de Tecido`system assigned`saqueado e de identidade gerida, respectivamente, que este recurso deve ter uma identidade implícita ( ) gerida.

### <a name="application-and-service-package"></a>Pacote de aplicação e serviço

1. Atualize o manifesto de aplicação para adicionar um elemento **Identidade Gerida** na secção **Principais,** contendo uma única entrada, como mostrado abaixo:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Isto mapeia a identidade atribuída à aplicação como recurso a um nome amigável, para posterior atribuição aos serviços que compõem a aplicação. 

2. Na secção **ServiceManifestImport** correspondente ao serviço que está a ser atribuído à identidade gerida, adicione um elemento **IdentityBindingPolicy,** conforme indicado abaixo:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Este elemento atribui a identidade da aplicação ao serviço; sem esta atribuição, o serviço não poderá aceder à identidade da aplicação. No corte acima, a `SystemAssigned` identidade (que é uma palavra-chave reservada) é mapeada `WebAdmin`para a definição do serviço sob o nome amigável .

3. Atualizar o manifesto de serviço para adicionar um elemento **Identidade Gerida** dentro `ServiceIdentityRef` da `IdentityBindingPolicy` secção **Recursos** com o nome correspondente ao valor da definição a partir da definição no manifesto de aplicação:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Este é o mapeamento equivalente de uma identidade para um serviço acima descrito, mas do ponto de vista da definição de serviço. A identidade é aqui referida pelo`WebAdmin`seu nome amigável , como declarado no manifesto de candidatura.

## <a name="next-steps"></a>Passos Seguintes
* Reveja [o suporte de identidade gerido](./concepts-managed-identity.md) no Tecido de Serviço Azure
* [Implementar um novo](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster azure service fabric com suporte de identidade gerido 
* [Ativar a identidade gerida](./configure-existing-cluster-enable-managed-identity-token-service.md) num cluster de tecido de serviço Azure existente
* Alavancar a identidade [gerida](./how-to-managed-identity-service-fabric-app-code.md) de uma aplicação de tecido de serviço a partir do código fonte
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceder um acesso à aplicação Azure Service Fabric a outros recursos azure](./how-to-grant-access-other-resources.md)
