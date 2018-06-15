---
title: Problema ao adicionar uma aplicação de galeria do Azure AD | Microsoft Docs
description: Compreender a letra de pessoas problemas comuns quando adicionar aplicações de galeria do Azure AD e o que pode fazer resolvê-los
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 5cb43976c17bfd46d1f16f0bdaff1c164bdc0fb6
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053764"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problema ao adicionar uma aplicação de galeria do Azure AD

Este artigo ajuda-o a compreender a letra de pessoas problemas comuns quando adicionar aplicações de galeria do Azure AD e o que pode fazer resolvê-los.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Posso clica no botão "Adicionar" e a minha aplicação demorava muito tempo a ser apresentado

Em algumas circunstâncias, pode demorar 1-2 minutos (e, por vezes, mais) para uma aplicação a aparecer depois adicioná-lo ao seu diretório. Embora não seja o desempenho esperado normal, pode ver a adição de aplicação está em curso, clicando no **notificações** ícone (de sino) no canto superior direito do [portal do Azure](https://portal.azure.com/) e procura para um **em curso** ou **concluído** notificação com a etiqueta **Criar aplicação.**

Se nunca é adicionada a sua aplicação ou encontrar um erro quando clicar no **adicionar** botão, verá um **notificação** num **erro** estado. Se quiser obter mais detalhes sobre o erro para obter mais informações para ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro, seguindo os passos a [como ver os detalhes de uma notificação de portal](#how-to-see-the-details-of-a-portal-notification) secção.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Posso clica no botão "Adicionar" e não aparece a minha aplicação

Por vezes, devido a problemas temporários, problemas de rede ou um erro, adicionar uma aplicação falhar. Pode dizer Isto acontece quando clicar no **notificações** ícone (de sino) no canto superior direito do portal do Azure e ver um ícone vermelho (!) junto à sua **Criar aplicação** notificação. Isto indica que ocorreu um erro ao criar a aplicação.

Se ocorrer um erro quando clicar no **adicionar** botão, verá um **notificação** num **erro** estado. Se quiser obter mais detalhes sobre o erro para obter mais informações para ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro, seguindo os passos a [como ver os detalhes de uma notificação de portal](#how-to-see-the-details-of-a-portal-notification) secção.

 ## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Posso não sabe como configurar a minha aplicação assim que tiver adicionado

Se precisar de ajuda para saber mais sobre as aplicações, o [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artigo é um bom local para iniciar.

Além disto, o [biblioteca de documentos de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) ajudá-lo a saber mais sobre o início de sessão com o Azure AD e como funciona.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação do portal

Pode ver os detalhes de qualquer notificação do portal, seguindo os passos abaixo:

1.  Clique em de **notificações** ícone (de sino) no canto superior direito do Portal do Azure

2.  Selecione qualquer notificação num **erro** Estado (as com um vermelho (!) junto ao-las).

    >[!NOTE]
    >Não é possível clicar as notificações de um **Successful** ou **em curso** estado.
    >
    >

4.  Utilize as informações em **detalhes de notificação** para obter mais detalhes sobre o problema de compreender.

5.  Se ainda precisar de ajuda, também pode partilhar estas informações com um engenheiro de suporte técnico ou o grupo do produto para obter ajuda com o seu problema.

6.  Clique em de **cópia** **ícone** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um engenheiro de grupo de suporte ou produto

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte

É muito importante que partilha **todos os detalhes apresentados abaixo** com um engenheiro de suporte se precisar de ajuda, para que o podem ajudar a rapidamente. Pode fazê-lo facilmente por **tirar uma captura de ecrã,** ou clicando a **ícone de erro de cópia**, encontrado para a direita do **copiar erro** caixa de texto.

## <a name="notification-details-explained"></a>Detalhes de notificação explicados

Consulte as seguintes descrições para obter mais detalhes sobre as notificações.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

-   **Título** – o título descritivo da notificação

  * Exemplo – **as definições de proxy de aplicações**

-   **Descrição** – a descrição, o que ocorreu em resultado da operação

    -   Exemplo – **interno url introduzido já está a ser utilizado por outra aplicação**

-   **ID de notificação** – o ID exclusivo da notificação

    -   Exemplo – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID do pedido de cliente** – o ID do pedido específico efetuado pelo browser

    -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Hora UTC de carimbo de data /** – timestamp durante os quais a notificação ocorreu, em UTC

    -   Exemplo – **2017-03-23T19:50:43.7583681Z**

-   **ID de transação interno** – o ID interno podemos utilizar ao procurar o erro do nosso sistemas

    -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – o utilizador que executou a operação

    -   Exemplo – **tperkins@f128.info**

-   **ID de inquilino** – o ID exclusivo do inquilino que o utilizador que executou a operação foi membro de

    -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID de objeto de utilizador** – o ID exclusivo do utilizador que executou a operação

    -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhada

-   **Nome a apresentar** – **(pode estar vazio)** mais detalhado nome a apresentar para o erro

    -   Exemplo – **as definições de proxy de aplicações**

-   **Estado** – o estado específico da notificação

    -   Exemplo – **falhou**

-   **ID de objeto** – **(pode estar vazio)** o ID de objeto relativamente ao qual foi efetuada a operação

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – a descrição, o que ocorreu em resultado da operação de detalhado

    -   Exemplo – **url interno 'http://bing.com/' é inválido porque já se encontra em utilização**

-   **Copiar erro** – clique em de **ícone copiar** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um grupo de suporte ou produto 
-   engenheiro

    -   Exemplo ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](manage-apps/what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
