---
title: Adicione uma identidade gerida a um tipo de nó de cluster gerido por Tecido de Serviço (pré-visualização)
description: Este artigo mostra como adicionar uma identidade gerida a um tipo de nó de cluster gerido por Tecido de Serviço
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 00e679b07a44b799b6ac6677201bb59eeddcd6cf
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841565"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Adicione uma identidade gerida a um tipo de nó de cluster gerido por Tecido de Serviço (pré-visualização)

Cada tipo de nó num cluster gerido por Tecido de Serviço é apoiado por um conjunto de escala de máquina virtual. Para permitir a utilização de identidades geridas com um tipo de nó de cluster gerido, foi adicionada uma propriedade `vmManagedIdentity` às definições do tipo nó que contém uma lista de identidades que podem ser utilizadas, `userAssignedIdentities` . A funcionalidade espelha como as identidades geridas podem ser usadas em clusters não geridos, tais como a utilização de uma identidade gerida com a [extensão de conjunto de escala de máquina virtual Azure Key Vault](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows).


Para um exemplo de uma implementação de cluster gerida por tecido de serviço que faz uso da identidade gerida num tipo de nó, consulte [este modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). Para obter uma lista de regiões apoiadas, consulte o [cluster gerido FAQ](https://docs.microsoft.com/azure/service-fabric/faq-managed-cluster#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Atualmente, apenas as identidades atribuídas ao utilizador são suportadas para esta funcionalidade.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* Se pretender utilizar o PowerShell, [instale](https://docs.microsoft.com/cli/azure/install-azure-cli) o CLI Azure para executar comandos de referência CLI.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Uma identidade gerida atribuída pelo utilizador pode ser definida na secção de recursos de um modelo Azure Resource Manager (ARM) para criação após a implementação:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

ou criado através do PowerShell:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Adicione uma atribuição de função com o Fornecedor de Recursos de Tecido de Serviço

Adicione uma atribuição de função à identidade gerida com a aplicação Service Fabric Resource Provider. Esta atribuição permite ao Service Fabric Resource Provider atribuir a identidade ao conjunto de escalas de máquina virtual do cluster gerido. 

Os seguintes valores devem ser utilizados, se for caso disso:

|Nome|Valor correspondente do Fornecedor de Recursos de Tecido de Serviço|
|----|-------------------------------------|
|ID da aplicação|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|ID de objeto|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Nome de definição de função|ID de definição de papel|
|----|-------------------------------------|
|Operador de identidade gerido|f1a07417-d97a-45cb-824c-7a7467783830
|



Esta atribuição de funções pode ser definida na secção de recursos utilizando o ID do objeto e iD de definição de função:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

ou criado através do PowerShell utilizando o ID da aplicação e o ID de definição de função:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

ou iD de objeto e iD de definição de papel:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Adicionar propriedades de identidade geridas à definição do tipo nó

Por fim, adicione as `vmManagedIdentity` propriedades e propriedades à `userAssignedIdentities` definição do nó do nó gerido:

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

Após a implementação, a identidade gerida criada foi adicionada ao conjunto de escala de máquina virtual do tipo nó designado e pode ser usado como esperado, tal como em qualquer cluster não gerido.

## <a name="troubleshooting"></a>Resolução de problemas

A não adição correta de uma atribuição de funções será satisfeita com o seguinte erro de implantação:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Erro de implementação do portal Azure mostrando o cliente com o ID de objeto/aplicação da SFRP não ter permissão para realizar atividade de gestão de identidade":::

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implementar uma aplicação para um cluster gerido por Tecido de Serviço](https://docs.microsoft.com/azure/service-fabric/tutorial-managed-cluster-deploy-app) 
