---
title: Problema de adição de uma aplicação da Galeria Azure AD [ Microsoft Docs
description: Compreenda os problemas comuns que as pessoas enfrentam ao adicionar aplicações da Azure AD Gallery e o que pode fazer para resolvê-los
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784434"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problema de adição de uma aplicação da Galeria AD Azure

Este artigo ajuda-o a compreender os problemas comuns que as pessoas enfrentam ao adicionar aplicações da Azure AD Gallery e o que pode fazer para os resolver.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Cliquei no botão "adicionar" e a minha aplicação demorou muito tempo a aparecer

Em algumas circunstâncias, pode levar 1-2 minutos (e às vezes mais) para que uma aplicação apareça depois de adicioná-la ao seu diretório. Embora este não seja o desempenho normal esperado, pode ver que a adição de aplicação está em andamento clicando no ícone **Notificações** (o sino) na parte superior direita do [portal Azure](https://portal.azure.com/) e procurando uma notificação **em progresso** ou **completa** com a etiqueta **de adição.**

Se a sua aplicação nunca for adicionada, ou se encontrar um erro ao clicar no botão **Adicionar,** verá uma **Notificação** num estado **de Erro.** Se quiser mais detalhes sobre o erro para saber mais ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro seguindo os passos na Como ver os detalhes de uma secção de [notificação do portal.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Cliquei no botão "adicionar" e a minha aplicação não apareceu

Por vezes, devido a problemas transitórios, problemas de networking ou um bug, adicionar uma aplicação falha. Pode dizer que isto acontece quando clica no ícone **Notificações** (o sino) na parte superior direita do portal Azure e vê um ícone vermelho (!) ao lado da notificação de **aplicação Adicionar.** Isto indica que houve um erro ao criar a aplicação.

Se encontrar um erro ao clicar no botão **Adicionar,** verá uma **Notificação** num estado **de Erro.** Se quiser mais detalhes sobre o erro para saber mais ou partilhar com um engenheiro de suporte, pode ver mais informações sobre o erro seguindo os passos na Como ver os detalhes de uma secção de [notificação do portal.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Não sei como configurar a minha candidatura depois de a adicionar.

Se precisar de ajuda para aprender sobre aplicações, a [Lista de Tutoriais sobre Como Integrar Apps SaaS com](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artigo de Diretório Ativo Azure é um bom lugar para começar.

Além disso, a Biblioteca de Documentos de [Aplicações AD Azure](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) ajuda-o a aprender mais sobre o único sign-on com a Azure AD e como funciona.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação do portal

Pode ver os detalhes de qualquer notificação do portal seguindo os passos abaixo:

1.  Selecione o ícone **notificações** (o sino) na parte superior direita do portal Azure

2.  Selecione qualquer notificação num estado **de Erro** (aqueles com um vermelho (!) ao lado deles).

    >[!NOTE]
    >Não é possível clicar em notificações num estado de **Sucesso** ou **Em Progresso.**
    >
    >

4.  Utilize as informações em Detalhes de **Notificação** para compreender mais detalhes sobre o problema.

5.  Se ainda precisar de ajuda, também pode partilhar esta informação com um engenheiro de suporte ou com o grupo de produtos para obter ajuda com o seu problema.

6.  Clique no **ícone** da **cópia** à direita da caixa de texto de **erro copiar** para copiar todos os detalhes de notificação para partilhar com um engenheiro de suporte ou grupo de produto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda enviando detalhes de notificação a um engenheiro de suporte

É muito importante que partilhe **todos os detalhes listados abaixo** com um engenheiro de suporte se precisar de ajuda, para que possam ajudá-lo rapidamente. Pode fazê-lo facilmente **tirando uma imagem,** ou clicando no ícone de **erro copy,** encontrado à direita da caixa de texto de **erro copy.**

## <a name="notification-details-explained"></a>Detalhes da notificação explicados

Consulte as seguintes descrições para obter mais detalhes sobre as notificações.

### <a name="essential-notification-items"></a>Itens essenciais de notificação

- **Título** – o título descritivo da notificação

  * Exemplo – Definições de **procuração de aplicação**

- **Descrição** – a descrição do que ocorreu em resultado da operação

  -   Exemplo – **O url interno introduzido já está a ser utilizado por outra aplicação**

- ID de **notificação** – o ID único da notificação

  -   Exemplo – **clienteNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID** de pedido de cliente – o ID de pedido específico feito pelo seu navegador

  -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Carimbo de tempo UTC** – o carimbo de tempo durante o qual ocorreu a notificação, na UTC

  -   Exemplo – **2017-03-23T19:50:43.7583681Z**

- **ID de Transação Interna** – o ID interno que podemos usar para procurar o erro nos nossos sistemas

  -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – o utilizador que realizou a operação

  -   Exemplo - **tperkins\@f128.info**

- **ID do inquilino** – a identificação única do inquilino que o utilizador que realizou a operação foi membro

  -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f962ece**

- **ID** do objeto do utilizador – o ID único do utilizador que realizou a operação

  -   Exemplo – **17f84be4-51f8-483a-b533-38379127a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

-   **Nome do ecrã** – **(pode estar vazio)** um nome de exibição mais detalhado para o erro

    -   Exemplo – Definições de **procuração de aplicação**

-   **Estado** – o estado específico da notificação

    -   Exemplo - **Falhado**

-   **ID do objeto** – **(pode estar vazio)** o ID do objeto contra o qual a operação foi realizada

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – a descrição detalhada do que ocorreu em resultado da operação

    -   Exemplo – **O url `https://bing.com/` interno é inválido uma vez que já está em uso**

-   **Erro de cópia** - Clique no ícone da **cópia** à direita da caixa de texto de **erro Copiar** para copiar todos os detalhes de notificação para partilhar com um suporte ou grupo de produto 
-   engenheiro

    -   Exemplo```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

