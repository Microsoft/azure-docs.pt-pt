---
title: Microsoft Graph APIs para PIM (Pré-visualização) - Azure AD / Microsoft Docs
description: Fornece informações sobre a utilização das APIs do Microsoft Graph para a Gestão de Identidade Privilegiada do Azure AD (PIM) (Pré-visualização).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75638668"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>ApIs do Microsoft Graph para Gestão de Identidade Privilegiada (Pré-visualização)

Pode executar todas as tarefas de Gestão de Identidade Privilegiada utilizando as [APIs](https://developer.microsoft.com/graph/docs/concepts/overview) do Microsoft Graph para o Diretório Ativo Azure. Este artigo descreve conceitos importantes para a utilização das APIs do Microsoft Graph para gestão de identidade privilegiada.

Para mais detalhes sobre as APIs do Microsoft Graph, consulte a referência a API de Gestão de Identidade Privilegiada do [Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> As APIs sob a versão /beta no Microsoft Graph estão em pré-visualização e estão sujeitas a alterações. A utilização destas APIs em aplicações de produção não é suportada.
>
> Embora todas as operações pim sejam suportadas através dos nossos comandos API gráficos, usamos um sistema diferente para aprovar pedidos de ativação. O Gráfico API para aprovação está atualmente a ser desenvolvido e será lançado ao longo dos próximos meses.

## <a name="required-permissions"></a>Permissões obrigatórias

Para ligar para as APIs do Microsoft Graph para gestão de identidade privilegiada, deve ter **uma ou mais** das seguintes permissões:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Definir permissões

Para que as aplicações liguem para as APIs do Microsoft Graph para gestão de identidade privilegiada, devem ter as permissões necessárias. A forma mais fácil de especificar as permissões necessárias é utilizar o quadro de [consentimento da AD Azure](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Definir permissões no Graph Explorer

Se estiver a utilizar o Graph Explorer para testar as suas chamadas, pode especificar as permissões na ferramenta.

1. Inscreva-se no [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) como administrador global.

1. Clique em **modificar permissões**.

    ![Graph Explorer - modificar permissões](./media/pim-apis/graph-explorer.png)

1. Selecione as caixas de verificação junto às permissões que pretende incluir. `PrivilegedAccess.ReadWrite.AzureAD`ainda não está disponível no Graph Explorer.

    ![Graph Explorer - modificar permissões](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Clique em **modificar permissões** para aplicar as alterações de permissão.

## <a name="next-steps"></a>Passos seguintes

- [Referência da AD AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
