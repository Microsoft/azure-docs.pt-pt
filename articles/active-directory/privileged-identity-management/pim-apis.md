---
title: Microsoft Graph APIs para PIM (visualização) – Azure Active Directory | Microsoft Docs
description: Fornece informações sobre como usar o PIM (Microsoft Graph APIs para Azure AD Privileged Identity Management) (versão prévia).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c7f42d536880f2578c62c6c4866b21be1cc9dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804551"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>APIs de Microsoft Graph para PIM (versão prévia)

Para a maioria das tarefas que você pode executar em Azure Active Directory (Azure AD) Privileged Identity Management (PIM) usando o portal do Azure, você também pode executar usando as [APIs de Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview). Este artigo descreve alguns conceitos importantes ao usar as APIs de Microsoft Graph para o PIM. Para obter detalhes sobre as APIs de Microsoft Graph, confira a [referência de API de Azure ad Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> As APIs na versão/beta no Microsoft Graph estão na visualização e estão sujeitas a alterações. Não há suporte para o uso dessas APIs em aplicativos de produção.

## <a name="required-permissions"></a>Permissões obrigatórias

Para chamar as APIs de Microsoft Graph para PIM, você deve ter **uma ou mais** das seguintes permissões:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Definir permissões

Para aplicativos chamarem as APIs de Microsoft Graph para PIM, eles devem ter as permissões necessárias. A maneira mais fácil de especificar as permissões necessárias é usar a [estrutura de consentimento do Azure ad](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Definir permissões no Gerenciador de gráficos

Se você estiver usando o explorador do Graph para testar suas chamadas, poderá especificar as permissões na ferramenta.

1. Entre no [Gerenciador de gráficos](https://developer.microsoft.com/graph/graph-explorer) como um administrador global.

1. Clique em **Modificar permissões**.

    ![Gerenciador de gráficos – modificar permissões](./media/pim-apis/graph-explorer.png)

1. Adicione marcas de seleção ao lado das permissões que você deseja incluir. `PrivilegedAccess.ReadWrite.AzureAD`Ainda não está disponível no explorador do Graph.

    ![Gerenciador de gráficos – modificar permissões](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Clique em **Modificar permissões** para aplicar as alterações de permissão.

## <a name="next-steps"></a>Passos seguintes

- [Referência de API de Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
