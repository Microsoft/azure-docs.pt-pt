---
title: Como criar e eliminar uma identidade gerida atribuído ao utilizador com o Azure Resource Manager
description: Instruções passo a passo obter instruções sobre como criar e eliminar atribuído ao utilizador gerido identidades com o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 611a2deccffeb142dc9a76bd260c9a8a080eb8bb
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816339"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Criar, listar e eliminar uma identidade gerida atribuído ao utilizador com o Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida no Azure Active Directory. Pode utilizar esta identidade para autenticar para serviços que suportam a autenticação do Azure AD, sem a necessidade de credenciais no seu código. 

Neste artigo, vai criar uma identidade gerida atribuído ao utilizador com um Azure Resource Manager.

Não é possível listar e eliminar uma identidade gerida atribuído ao utilizador com um modelo Azure Resource Manager.  Veja os artigos seguintes para criar e a identidade gerida de lista um atribuído ao utilizador:

- [Lista atribuído ao utilizador com identidade gerida](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Eliminar utilizador atribuído a identidade gerida](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="template-creation-and-editing"></a>Criação de modelos e a edição

Como com o Azure portal e criação de scripts, os modelos Azure Resource Manager fornecem a capacidade de implementar recursos novos ou modificados definidos por um grupo de recursos do Azure. Várias opções estão disponíveis para edição de modelo e a implantação, local e baseado no portal, incluindo:

- Utilizar um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite-lhe criar um modelo a partir do zero ou baseá-la num comum existente ou [modelo de início rápido](https://azure.microsoft.com/documentation/templates/).
- Derivar de um grupo de recursos existente ao exportar um modelo a partir de qualquer uma [implementação original](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), ou a partir do [estado atual da implantação](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Com um local [editor de JSON (por exemplo, o VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregar e implementar com o PowerShell ou a CLI.
- Com o Visual Studio [projeto do grupo de recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) para criar e implementar um modelo. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

Para criar uma identidade gerida atribuído ao utilizador, utilize o modelo seguinte. Substitua o `<USER ASSIGNED IDENTITY NAME>` valor pelos seus próprios valores:

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
## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como atribuir um atribuído ao utilizador a identidade para a VM do Azure com uma veja de modelo do Azure Resource Manager, gerido [configurar geridos identidades para recursos do Azure na VM do Azure com um modelo de](qs-configure-template-windows-vm.md).


 
