---
title: APIs de Gráfico microsoft para PIM (Pré-visualização) - Azure AD / Microsoft Docs
description: Fornece informações sobre a utilização das APIs do Gráfico da Microsoft para Azure AD Gestão de Identidade Privilegiada (PIM) (Preview).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb102c23d80095b8eb79fc25d1c1fd9d7f374fce
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529699"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>APIs do Gráfico da Microsoft para Gestão de Identidade Privilegiada (Pré-visualização)

Pode executar tarefas privilegiadas de Gestão de Identidade utilizando as [APIs](/graph/overview) do Gráfico da Microsoft para o Azure Ative Directory. Este artigo descreve conceitos importantes para a utilização das APIs do Gráfico da Microsoft para gestão de identidade privilegiada.

Para mais informações sobre as APIs do Gráfico da Microsoft, consulte a [referência Azure AD Privileged Identity Management API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta).

> [!IMPORTANT]
> As APIs na versão /beta no Microsoft Graph estão em pré-visualização e estão sujeitas a alterações. A utilização destas APIs em aplicações de produção não é suportada.

## <a name="required-permissions"></a>Permissões obrigatórias

Para ligar para as APIs do Gráfico da Microsoft para gestão de identidade privilegiada, tem de ter **uma ou mais** das seguintes permissões:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Definir permissões

Para que as aplicações liguem para as APIs do Gráfico da Microsoft para gestão de identidade privilegiada, devem ter as permissões necessárias. A forma mais fácil de especificar as permissões necessárias é utilizar o [quadro de consentimento Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Definir permissões no Graph Explorer

Se estiver a utilizar o Graph Explorer para testar as suas chamadas, pode especificar as permissões na ferramenta.

1. Inscreva-se no [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) como Administrador Global.

1. Clique **em modificar permissões.**

    ![Screenshot que mostra a página "Graph Explorer" com a ação "modificar permissões" selecionada.](./media/pim-apis/graph-explorer.png)

1. Selecione as caixas de verificação ao lado das permissões que pretende incluir. `PrivilegedAccess.ReadWrite.AzureAD` ainda não está disponível no Graph Explorer.

    ![Graph Explorer - modificar permissões](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Clique **em Modificar Permissões** para aplicar as alterações de permissão.

## <a name="next-steps"></a>Próximos passos

- [Referência da Azure AD Gestão de Identidade Privilegiada API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)