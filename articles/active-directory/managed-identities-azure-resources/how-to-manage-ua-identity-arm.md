---
title: Criar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Gestor de Recursos Azure
description: Instruções passo a passo sobre como criar e eliminar identidades geridas atribuídas pelo utilizador utilizando o Gestor de Recursos Azure.
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
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253380"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Gestor de Recursos Azure


Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar serviços que suportam a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, cria-se uma identidade gerida atribuída ao utilizador através de um Gestor de Recursos Azure.

Não é possível listar e eliminar uma identidade gerida atribuída pelo utilizador usando um modelo de Gestor de Recursos Azure.  Consulte os seguintes artigos para criar e listar uma identidade gerida atribuída pelo utilizador:

- [Lista identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Eliminar identidade gerida atribuída ao utilizador](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tem uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="template-creation-and-editing"></a>Criação e edição de modelos

Tal como acontece com o portal Azure e a escrita, os modelos do Gestor de Recursos Azure fornecem a capacidade de implantar recursos novos ou modificados definidos por um grupo de recursos Azure. Várias opções estão disponíveis para edição e implementação de modelos, tanto locais como baseados em portal, incluindo:

- Utilizando um [modelo personalizado do Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)que lhe permite criar um modelo de raiz, ou baseá-lo num modelo comum ou [quickStart](https://azure.microsoft.com/documentation/templates/)existente.
- Derivado de um grupo de recursos existente, exportando um modelo da [implantação original,](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)ou do [estado atual da implantação.](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)
- Utilizando um [editor local da JSON (como o Código VS)](../../azure-resource-manager/resource-manager-create-first-template.md)e, em seguida, carregar e implementar utilizando powerShell ou CLI.
- Utilizando o projeto Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para criar e implementar um modelo. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para criar uma identidade gerida atribuída ao utilizador, utilize o seguinte modelo. Substitua o valor `<USER ASSIGNED IDENTITY NAME>` por valores próprios:

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

Para obter informações sobre como atribuir uma identidade gerida atribuída ao utilizador a um VM Azure utilizando um modelo de Gestor de Recursos Azure, [configurar identidades geridas para recursos Azure num VM Azure utilizando um modelo](qs-configure-template-windows-vm.md).


 
