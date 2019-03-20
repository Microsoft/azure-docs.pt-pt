---
title: Microsoft Graph APIs PIM (pré-visualização) | Documentos da Microsoft
description: Fornece informações sobre como utilizar o Microsoft Graph APIs para o Azure Active Directory Privileged Identity Management (PIM) (pré-visualização).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d853579335653987321fded154eb9fb0f8dbdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000297"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>Microsoft Graph APIs PIM (pré-visualização)

Para a maioria das tarefas pode executar no Azure AD Privileged Identity Management (PIM) com o portal do Azure, também pode efetuar a utilizar o [Microsoft Graph APIs](https://developer.microsoft.com/graph/docs/concepts/overview). Este artigo descreve alguns conceitos importantes ao utilizar o Microsoft Graph APIs PIM. Para obter detalhes sobre as APIs do Microsoft Graph, verifique os [referência de API do Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> APIs sob a versão de /beta no Microsoft Graph estão em pré-visualização e estão sujeitos a alterações. Não é suportada a utilização destas API em aplicações de produção.

## <a name="required-permissions"></a>Permissões obrigatórias

Para chamar as APIs do Microsoft Graph PIM, tem de ter **um ou mais** as permissões seguintes:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Definir permissões

Para aplicativos chamar as APIs do Microsoft Graph PIM, tem de ter as permissões necessárias. A maneira mais fácil para especificar as permissões necessárias é utilizar o [framework de consentimento do Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Definir permissões na API do Graph

Se estiver a utilizar o Explorador do gráfico para testar as suas chamadas, pode especificar as permissões na ferramenta.

1. Inicie sessão no [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) como um Administrador Global.

1. Clique em **modificar permissões**.

    ![Explorador do gráfico - modificar permissões](./media/pim-apis/graph-explorer.png)

1. Adicione marcas de verificação junto às permissões que pretende incluir. `PrivilegedAccess.ReadWrite.AzureAD` ainda não está disponível na API do Graph.

    ![Explorador do gráfico - modificar permissões](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Clique em **modificar permissões** para aplicar as alterações de permissão.

## <a name="next-steps"></a>Passos Seguintes

- [Referência do Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
