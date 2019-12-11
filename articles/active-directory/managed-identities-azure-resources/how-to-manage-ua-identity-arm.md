---
title: Criar & excluir uma identidade gerenciada atribuída pelo usuário usando Azure Resource Manager
description: Instruções passo a passo sobre como criar e excluir identidades gerenciadas atribuídas pelo usuário usando o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f113366363de40f8934a9db6cb3a82cc477f03
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996974"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando Azure Resource Manager


Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em serviços que dão suporte à autenticação do Azure AD, sem precisar de credenciais em seu código. 

Neste artigo, você cria uma identidade gerenciada atribuída pelo usuário usando um Azure Resource Manager.

Não é possível listar e excluir uma identidade gerenciada atribuída pelo usuário usando um modelo de Azure Resource Manager.  Consulte os seguintes artigos para criar e listar uma identidade gerenciada atribuída pelo usuário:

- [Listar identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Excluir identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="template-creation-and-editing"></a>Criação e edição de modelo

Assim como ocorre com o portal do Azure e o script, os modelos de Azure Resource Manager fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Várias opções estão disponíveis para edição e implantação de modelos, tanto locais quanto baseadas em portal, incluindo:

- Usando um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite que você crie um modelo a partir do zero ou baseie-o em um modelo comum ou de [início rápido](https://azure.microsoft.com/documentation/templates/)existente.
- Derivando de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)ou do [estado atual da implantação](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Usando um [Editor de JSON local (como vs Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregando e implantando usando o PowerShell ou a CLI.
- Usando o projeto do [grupo de recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar um modelo. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Para criar uma identidade gerenciada atribuída pelo usuário, use o modelo a seguir. Substitua o valor `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atribuir uma identidade gerenciada atribuída pelo usuário a uma VM do Azure usando um modelo de Azure Resource Manager, confira [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos](qs-configure-template-windows-vm.md).


 
