---
title: 'Sincronização do Azure AD Connect: Extensões de diretórios | Documentos da Microsoft'
description: Este tópico descreve a funcionalidade de extensões de diretório no Azure AD Connect.
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
ms.openlocfilehash: ff0fd4d01eab739b79685c1de67cb8fe28873961
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347992"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: Extensões de diretórios
Pode utilizar extensões de diretório para expandir o esquema no Azure Active Directory (Azure AD) com seus próprios atributos do Active Directory no local. Esta funcionalidade permite-lhe criar aplicações LOB ao consumo de atributos que continua a gerir no local. Estes atributos podem ser consumidos por meio [extensões de diretório do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://developer.microsoft.com/graph/). Pode ver os atributos disponíveis utilizando [do Azure AD Graph](https://graphexplorer.azurewebsites.net/) e [de API do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

Configurar quais atributos adicionais que pretende sincronizar no caminho de definições personalizadas no Assistente de instalação.

>[!NOTE]
>A caixa de atributos disponíveis diferencia maiúsculas de minúsculas.

![Assistente de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A instalação mostra os seguintes atributos, são candidatos válidos:

* Tipos de objeto de utilizador e grupo
* Atributos de valor único: Cadeia, booleano, número inteiro, binário
* Atributos com múltiplos valores: Cadeia de caracteres, binário


>[!NOTE]
> Embora o Azure AD Connect suporta a sincronização do Active Directory com múltiplos valores de atributos para o Azure AD como extensões de diretório com múltiplos valores, não é atualmente possível obter/consumir os dados carregados em atributos de extensão de diretório com múltiplos valores.

A lista de atributos é lidos a partir da cache do esquema que é criada durante a instalação do Azure AD Connect. Se tiver expandido o esquema do Active Directory com atributos adicionais, terá [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema) antes destes novos atributos estão visíveis.

Um objeto no Azure AD pode ter até 100 atributos de extensões de diretório. O comprimento máximo é 250 carateres. Se um valor de atributo superior, o motor de sincronização trunca-lo.

Durante a instalação do Azure AD Connect, um aplicativo está registado em que esses atributos estão disponíveis. Pode ver esta aplicação no portal do Azure.

![Aplicação de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Os atributos são prefixados com a extensão \_{AppClientId}\_. AppClientId tem o mesmo valor para todos os atributos no inquilino do Azure AD.

Esses atributos estão agora disponíveis por meio da API do Azure AD Graph. Pode consultá-los usando [do Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![API do Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Ou pode consultar os atributos através do Microsoft Graph API, utilizando [API do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Terá de pedir os atributos a serem retornados. Selecionar explicitamente os atributos como este: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Para obter mais informações, consulte [Microsoft Graph: Utilizar parâmetros de consulta](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
