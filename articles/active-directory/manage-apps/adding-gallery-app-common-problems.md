---
title: Problema ao adicionar uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Compreender os rostos de pessoas de problemas comuns ao adicionar aplicações de galeria do Azure AD e o que pode fazer para resolvê-los
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: a898b5b235099109fcfeaaa4d647493e54caf57e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853176"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problema ao adicionar uma aplicação da galeria do Azure AD

Este artigo ajuda-o a compreender os rostos de pessoas de problemas comuns ao adicionar aplicações de galeria do Azure AD e o que pode fazer para resolvê-los.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Cliquei no botão "Adicionar" e a minha aplicação demorava muito tempo para que sejam apresentadas

Em algumas circunstâncias, pode demorar 1 a 2 minutos (e, às vezes, mais tempo) para uma aplicação para aparecerem ao fim de adicioná-lo ao seu diretório. Embora não seja o desempenho esperado normal, pode ver a adição de aplicação está em curso, ao clicar no **notificações** ícone (na campainha) no canto superior direito dos [portal do Azure](https://portal.azure.com/) e à procura para uma **em curso** ou **concluído** notificação rotulada **a adicionar a aplicação.**

Se seu aplicativo nunca é adicionado ou encontrar um erro ao clicar o **Add** botão, verá um **notificação** num **erro** estado. Se quiser obter mais detalhes sobre o erro para obter mais informações para ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro ao seguir os passos a [como ver os detalhes de uma notificação no portal](#how-to-see-the-details-of-a-portal-notification) secção.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Cliquei no botão "Adicionar" e meu aplicativo não foi publicado

Às vezes, devido a problemas transitórios, problemas de rede ou um bug, adicionar uma aplicação falha. Pode dizer isso acontece quando clica o **notificações** ícone (na campainha) no canto superior direito do portal do Azure e que veja um ícone vermelho (!!!) junto ao seu **a adicionar a aplicação** notificação. Isto indica que ocorreu um erro ao criar a aplicação.

Se ocorrer um erro ao clicar o **Add** botão, verá um **notificação** num **erro** estado. Se quiser obter mais detalhes sobre o erro para obter mais informações para ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro ao seguir os passos a [como ver os detalhes de uma notificação no portal](#how-to-see-the-details-of-a-portal-notification) secção.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Não sei como configurar a minha aplicação assim que tiver adicionado

Se precisar de ajuda a aprender sobre aplicativos, o [lista de tutoriais sobre como integrar aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artigo é um bom lugar para começar.

Além disso, o [biblioteca de documentos de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) ajuda-o a saber mais sobre o início de sessão único com o Azure AD e como ela funciona.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação no portal

Pode ver os detalhes de qualquer notificação do portal, seguindo os passos abaixo:

1.  Selecione o **notificações** ícone (de sino) na parte superior direita do Portal do Azure

2.  Selecione qualquer notificação numa **erro** Estado (aquelas com linhas vermelhas (!!!) junto a eles).

    >[!NOTE]
    >Não é possível clicar em notificações numa **Successful** ou **em curso** estado.
    >
    >

4.  Utilize as informações em **detalhes de notificação** para obter mais detalhes sobre o problema de compreender.

5.  Se ainda precisar de ajuda, também pode partilhar estas informações com um engenheiro de suporte ou o grupo de produto para obter ajuda com o seu problema.

6.  Clique nas **cópia** **ícone** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um engenheiro de grupo de suporte ou o produto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte

É muito importante que partilham **todos os detalhes indicados abaixo** com um engenheiro de suporte se precisar de ajuda, para que eles podem ajudá-lo a rapidamente. Pode fazê-lo facilmente ao **tirar uma captura de ecrã** ou ao clicar o **ícone de erro da cópia**, apresentado à direita do **copiar erro** caixa de texto.

## <a name="notification-details-explained"></a>Detalhes da notificação explicados

Consulte as seguintes descrições para obter mais detalhes sobre as notificações.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

- **Título** – o título descritivo da notificação

  * Exemplo – **as definições de proxy de aplicação**

- **Descrição** – a descrição, o que ocorreu em resultado da operação

  -   Exemplo – **interno url introduzido já está a ser utilizado por outra aplicação**

- **ID de notificação** – o ID exclusivo da notificação

  -   Exemplo – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID do pedido de cliente** – o ID de pedido específico feito pelo browser

  -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Hora UTC do carimbo de data /** – o período de tempo durante o qual a notificação ocorreu, em UTC

  -   Exemplo – **2017-03-23T19:50:43.7583681Z**

- **ID de transação interno** – o ID interno, podemos usar para procurar o erro nos nossos sistemas

  -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – o utilizador que executou a operação

  -   Exemplo – **tperkins\@f128.info**

- **ID do inquilino** – o ID exclusivo do inquilino que fazia parte, o utilizador que executou a operação

  -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **ID de objeto de utilizador** – o ID exclusivo do utilizador que executou a operação

  -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhada

-   **Nome a apresentar** – **(pode estar vazio)** um nome a apresentar mais detalhado do erro

    -   Exemplo – **as definições de proxy de aplicação**

-   **Estado** – o estado específico da notificação

    -   Exemplo – **falhou**

-   **ID de objeto** – **(pode estar vazio)** o ID de objeto em relação aos quais a operação foi executada

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – o detalhadas descrição, o que ocorreu em resultado da operação

    -   Exemplo – **url interno `https://bing.com/` é inválido, uma vez que já está em utilização**

-   **Erro ao copiar** – clique a **ícone de cópia** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um grupo de suporte ou produto 
-   engenheiro

    -   Exemplo ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

