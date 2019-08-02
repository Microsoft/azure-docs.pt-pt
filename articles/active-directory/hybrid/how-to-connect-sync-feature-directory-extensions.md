---
title: 'Sincronização de Azure AD Connect: Extensões de diretório | Microsoft Docs'
description: Este tópico descreve o recurso de extensões de diretório no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fdfce58bdd8e13637e77d01d4b6c0ab21f696a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607647"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização de Azure AD Connect: Extensões de diretório
Você pode usar extensões de diretório para estender o esquema no Azure Active Directory (Azure AD) com seus próprios atributos do Active Directory local. Esse recurso permite que você crie aplicativos LOB consumindo atributos que você continua a gerenciar localmente. Esses atributos podem ser consumidos por meio [do Azure AD API do Graph extensões de diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://developer.microsoft.com/graph/). Você pode ver os atributos disponíveis usando o [Explorador do Graph do Azure ad](https://graphexplorer.azurewebsites.net/) e o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer), respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.

>[!NOTE]
>A caixa atributos disponíveis diferencia maiúsculas de minúsculas.

![Assistente de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objeto de usuário e grupo
* Atributos de valor único: Cadeia de caracteres, booliano, inteiro, binário
* Atributos com valores múltiplos: Cadeia de caracteres, binário


>[!NOTE]
> Embora Azure AD Connect ofereça suporte à sincronização de atributos de Active Directory de valores múltiplos para o Azure AD como extensões de diretório com vários valores, atualmente não há nenhuma maneira de recuperar/consumir os dados carregados em atributos de extensão de diretório com valores múltiplos.

A lista de atributos é lida do cache de esquema que é criado durante a instalação do Azure AD Connect. Se você estendeu o esquema de Active Directory com atributos adicionais, deverá [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema) antes que esses novos atributos fiquem visíveis.

Um objeto no Azure AD pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for maior, o mecanismo de sincronização o truncará.

Durante a instalação do Azure AD Connect, um aplicativo é registrado onde esses atributos estão disponíveis. Você pode ver esse aplicativo na portal do Azure.

![Aplicativo de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Os atributos são prefixados com a \_extensão {AppClientId\_}. AppClientId tem o mesmo valor para todos os atributos em seu locatário do Azure AD.

Esses atributos agora estão disponíveis por meio do API do Graph do Azure AD. Você pode consultá-los usando o [Explorador do Azure ad Graph](https://graphexplorer.azurewebsites.net/).

![Explorador do Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Ou você pode consultar os atributos por meio da API Microsoft Graph, usando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Você precisa solicitar que os atributos sejam retornados. Selecione explicitamente os atributos como este: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $Select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Para obter mais informações, [consulte Microsoft Graph: Use parâmetros](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)de consulta.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a configuração de [sincronização de Azure ad Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
