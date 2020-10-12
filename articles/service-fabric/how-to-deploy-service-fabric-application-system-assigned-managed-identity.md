---
title: Implementar uma aplicação de Tecido de Serviço com MI atribuído ao sistema
description: Este artigo mostra-lhe como atribuir uma identidade gerida atribuída ao sistema a uma aplicação de Tecido de Serviço Azure
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415658"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Implementar aplicação de Tecido de Serviço com identidade gerida atribuída ao sistema

Para aceder à funcionalidade de identidade gerida para aplicações Azure Service Fabric, deve primeiro ativar o Serviço de Token de Identidade Gerida no cluster. Este serviço é responsável pela autenticação de aplicações do Service Fabric utilizando as suas identidades geridas, e pela obtenção de fichas de acesso em seu nome. Uma vez que o serviço esteja ativado, pode vê-lo no Service Fabric Explorer sob a secção **Sistema** no painel esquerdo, funcionando sob o tecido de **nome:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE] 
> A implementação de aplicações de Tecido de Serviço com identidades geridas é suportada a partir da versão API `"2019-06-01-preview"` . Também pode utilizar a mesma versão API para tipo de aplicação, versão tipo de aplicação e recursos de serviço. O tempo de funcionação mínimo suportado do tecido de serviço é de 6,5 CU2. Na additoina, o ambiente de construção/pacote também deve ter o SF .Net SDK em CU2 ou superior

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

### <a name="application-template"></a>Modelo de aplicação

Para ativar a aplicação com uma identidade gerida atribuída pelo sistema, adicione a propriedade **de identidade** ao recurso de aplicação, com **sistema tipoAssigned** como mostrado no exemplo abaixo:

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
Esta propriedade declara (ao Azure Resource Manager, e aos Fornecedores de Recursos de Identidade e Tecido de Serviço Geridos, respectivamente, que este recurso terá uma `system assigned` identidade implícita () gerida.

### <a name="application-and-service-package"></a>Pacote de aplicação e serviço

1. Atualizar o manifesto de aplicação para adicionar um elemento **ManagedIdentity** na secção **Principais,** contendo uma única entrada como mostrado abaixo:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Isto mapeia a identidade atribuída à aplicação como recurso a um nome amigável, para posterior atribuição aos serviços que compõem a aplicação. 

2. Na secção **ServiceManifestImport** correspondente ao serviço que está a ser atribuído a identidade gerida, adicione um elemento **IdentityBindingPolicy,** conforme indicado abaixo:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Este elemento atribui a identidade do pedido ao serviço; sem esta atribuição, o serviço não poderá aceder à identidade da aplicação. No corte acima, a `SystemAssigned` identidade (que é uma palavra-chave reservada) é mapeada para a definição do serviço sob o nome amigável `WebAdmin` .

3. Atualizar o manifesto de serviço para adicionar um elemento **ManagedIdentity** dentro da secção **Recursos** com o nome correspondente ao valor da `ServiceIdentityRef` definição a partir da `IdentityBindingPolicy` definição no manifesto de aplicação:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Este é o mapeamento equivalente de uma identidade a um serviço como descrito acima, mas do ponto de vista da definição de serviço. A identidade é aqui referenciada pelo seu nome amigável, `WebAdmin` conforme declarado no manifesto de candidatura.

## <a name="next-steps"></a>Passos Seguintes
* Rever [suporte de identidade gerido](./concepts-managed-identity.md) em Azure Service Fabric
* [Implementar um novo](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster de tecido de serviço Azure com suporte de identidade gerido 
* [Ativar a identidade gerida](./configure-existing-cluster-enable-managed-identity-token-service.md) num cluster de tecido de serviço Azure existente
* Alavancar a identidade [gerida](./how-to-managed-identity-service-fabric-app-code.md) de uma aplicação de Tecido de Serviço a partir do código fonte
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceder a uma aplicação Azure Service Fabric acesso a outros recursos da Azure](./how-to-grant-access-other-resources.md)
