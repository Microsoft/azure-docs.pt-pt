---
title: 'Sincronização de Azure AD Connect: extensões de diretório | Microsoft Docs'
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
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 138ca9bf3352c46b8ac495b58a2fd6d7bafeb658
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889857"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização de Azure AD Connect: extensões de diretório
Você pode usar extensões de diretório para estender o esquema no Azure Active Directory (Azure AD) com seus próprios atributos do Active Directory local. Esse recurso permite que você crie aplicativos LOB consumindo atributos que você continua a gerenciar localmente. Esses atributos podem ser consumidos por meio [do Azure AD API do Graph extensões de diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://developer.microsoft.com/graph/). Você pode ver os atributos disponíveis usando o [Explorador do Graph do Azure ad](https://graphexplorer.azurewebsites.net/) e o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer), respectivamente. Você também pode usar esse recurso para criar grupos dinâmicos no Azure AD.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalizar quais atributos sincronizar com o Azure AD

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.

>[!NOTE]
>A caixa atributos disponíveis diferencia maiúsculas de minúsculas.

![Assistente de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objeto de usuário e grupo
* Atributos de valor único: cadeia de caracteres, booliano, inteiro, binário
* Atributos com valores múltiplos: cadeia de caracteres, binário


>[!NOTE]
> Embora Azure AD Connect ofereça suporte à sincronização de atributos de Active Directory de valores múltiplos para o Azure AD como extensões de diretório com vários valores, atualmente não há nenhuma maneira de recuperar/consumir os dados carregados em atributos de extensão de diretório com valores múltiplos.

A lista de atributos é lida do cache de esquema que é criado durante a instalação do Azure AD Connect. Se você estendeu o esquema de Active Directory com atributos adicionais, deverá [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema) antes que esses novos atributos fiquem visíveis.

Um objeto no Azure AD pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for maior, o mecanismo de sincronização o truncará.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Alterações de configuração no Azure AD feitas pelo assistente

Durante a instalação do Azure AD Connect, um aplicativo é registrado onde esses atributos estão disponíveis. Você pode ver esse aplicativo na portal do Azure. Seu nome é sempre **aplicativo de extensão do esquema de locatário**.

![Aplicativo de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Certifique-se de selecionar **todos os aplicativos** para ver esse aplicativo.

Os atributos são prefixados com a **extensão \_{ApplicationId}\_** . ApplicationId tem o mesmo valor para todos os atributos em seu locatário do Azure AD. Você precisará desse valor para todos os outros cenários neste tópico.

## <a name="viewing-attributes-using-graph"></a>Exibindo atributos usando o grafo

Esses atributos agora estão disponíveis por meio do API do Graph do Azure AD. Você pode consultá-los usando o [Explorador do Azure ad Graph](https://graphexplorer.azurewebsites.net/).

![Explorador do Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Ou você pode consultar os atributos por meio da API Microsoft Graph, usando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> No Microsoft Graph, você precisa solicitar que os atributos sejam retornados. Selecione explicitamente os atributos como este: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division.
>
> Para obter mais informações, consulte [Microsoft Graph: usar parâmetros de consulta](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Usar os atributos em grupos dinâmicos

Um dos cenários mais úteis é usar esses atributos em segurança dinâmica ou grupos do Office 365.

1. Crie um novo grupo no Azure AD. Dê a ele um bom nome e verifique se o **tipo de associação** é **Dynamic User**.

   ![Captura de tela com um novo grupo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecione para **Adicionar consulta dinâmica**. Se você examinar as propriedades, não verá esses atributos estendidos. Você precisa adicioná-los primeiro. Clique em **obter propriedades de extensão personalizadas**, insira a ID do aplicativo e clique em **Atualizar Propriedades**.

   ![Captura de tela em que as extensões de diretório foram adicionadas](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Abra a lista suspensa Propriedade e observe que os atributos que você adicionou agora estão visíveis.

   ![Captura de tela com novos atributos exibidos na interface do usuário](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Conclua a expressão para atender às suas necessidades. Em nosso exemplo, a regra é definida como **(User. extension_9d98ed114c4840d298fad781915f27e4_division-EQ "Sales and marketing")** .

4. Depois que o grupo tiver sido criado, dê ao Azure AD algum tempo para preencher os membros e, em seguida, examine os membros.

   ![Captura de tela com membros no grupo dinâmico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização de Azure ad Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
