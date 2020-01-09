---
title: APIs de Microsoft Graph para PIM (visualização)-Azure AD | Microsoft Docs
description: Fornece informações sobre como usar o PIM (Microsoft Graph APIs para Azure AD Privileged Identity Management) (versão prévia).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da6bffbc54bfa6e9c39ddace665eb7cfec58614
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638668"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>APIs de Microsoft Graph para Privileged Identity Management (versão prévia)

Você pode executar todas as tarefas de Privileged Identity Management usando as [APIs de Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview) para Azure Active Directory. Este artigo descreve conceitos importantes para usar as APIs de Microsoft Graph para Privileged Identity Management.

Para obter detalhes sobre as APIs de Microsoft Graph, confira a [referência de API de Azure ad Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> As APIs na versão/beta no Microsoft Graph estão na visualização e estão sujeitas a alterações. Não há suporte para o uso dessas APIs em aplicativos de produção.
>
> Embora todas as operações de PIM tenham suporte por meio de nossos comandos de API do Graph, usamos um sistema diferente para aprovar as solicitações de ativação. A API do Graph para aprovação está sendo desenvolvida no momento e será lançada nos próximos meses.

## <a name="required-permissions"></a>Permissões obrigatórias

Para chamar as APIs de Microsoft Graph para Privileged Identity Management, você deve ter **uma ou mais** das seguintes permissões:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Definir permissões

Para aplicativos chamarem as APIs de Microsoft Graph para Privileged Identity Management, eles devem ter as permissões necessárias. A maneira mais fácil de especificar as permissões necessárias é usar a [estrutura de consentimento do Azure ad](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Definir permissões no Gerenciador de gráficos

Se você estiver usando o explorador do Graph para testar suas chamadas, poderá especificar as permissões na ferramenta.

1. Entre no [Gerenciador de gráficos](https://developer.microsoft.com/graph/graph-explorer) como um administrador global.

1. Clique em **Modificar permissões**.

    ![Gerenciador de gráficos – modificar permissões](./media/pim-apis/graph-explorer.png)

1. Marque as caixas de seleção ao lado das permissões que você deseja incluir. o `PrivilegedAccess.ReadWrite.AzureAD` ainda não está disponível no explorador do Graph.

    ![Gerenciador de gráficos – modificar permissões](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Clique em **Modificar permissões** para aplicar as alterações de permissão.

## <a name="next-steps"></a>Passos seguintes

- [Referência de API de Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
