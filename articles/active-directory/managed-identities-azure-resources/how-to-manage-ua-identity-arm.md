---
title: Crie & eliminar uma identidade gerida atribuída ao utilizador utilizando o Gestor de Recursos Azure
description: Instruções passo a passo sobre como criar e eliminar identidades geridas atribuídas pelo utilizador usando o Gestor de Recursos Azure.
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
ms.openlocfilehash: c2ff31d603cc761c25645d80c4e2e7635ad05e00
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84694370"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Gestor de Recursos Azure


Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida no Azure Ative Directory. Pode utilizar esta identidade para autenticar serviços que suportem a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, cria uma identidade gerida atribuída ao utilizador através de um Gestor de Recursos Azure.

Não é possível listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando um modelo de Gestor de Recursos Azure.  Consulte os seguintes artigos para criar e listar uma identidade gerida atribuída pelo utilizador:

- [Lista identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Eliminar identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="template-creation-and-editing"></a>Criação e edição de modelos

Tal como acontece com o portal Estruição e scripts Azure, os modelos do Azure Resource Manager fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos Azure. Várias opções estão disponíveis para edição e implementação de modelos, tanto locais como baseados em portal, incluindo:

- Utilizando um [modelo personalizado do Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)que permite criar um modelo de raiz, ou baseá-lo num modelo comum ou [de arranque rápido](https://azure.microsoft.com/documentation/templates/)existente.
- Derivado de um grupo de recursos existente, exportando um modelo a partir da [implantação original,](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)ou do [estado atual da implantação](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Utilizando um editor JSON local [(como o VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, fazer upload e implantação utilizando o PowerShell ou o CLI.
- Utilizando o projeto Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para criar e implementar um modelo. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para criar uma identidade gerida atribuída ao utilizador, utilize o seguinte modelo. Substitua o `<USER ASSIGNED IDENTITY NAME>` valor pelos seus próprios valores:

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

Para obter informações sobre como atribuir uma identidade gerida atribuída ao utilizador a um VM Azure usando um modelo de Gestor de Recursos Azure ver, [Configure identidades geridas para recursos Azure em um VM Azure usando um modelo](qs-configure-template-windows-vm.md).


 
