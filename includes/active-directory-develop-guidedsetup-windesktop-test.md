---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2325509f68ced7c66d9f733b07247ae01301b565
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181545"
---
## <a name="test-your-code"></a>Teste o seu código

Para executar o seu projeto, no Estúdio Visual, selecione **F5**. A sua aplicação **MainWindow** é apresentada, como mostrado aqui:

![Teste a sua candidatura](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

A primeira vez que executa a aplicação e seleciona o botão **Call Microsoft Graph API,** é-lhe pedido que faça o início. Utilize uma conta Azure Ative Diretório (conta de trabalho ou escola) ou uma conta Microsoft (live.com, outlook.com) para testá-la.

![Iniciar sessão na aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que inscreveu a sua candidatura, também é solicitado a dar consentimento para permitir que a aplicação aceda ao seu perfil e instime-o, como mostrado aqui:

![Forneça o seu consentimento para o acesso à aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, deverá consultar as informações de perfil do utilizador que são devolvidas pela chamada para a Microsoft Graph API. Os resultados são apresentados na caixa de Resultados de **Chamada API.** Informações básicas sobre o símbolo que `AcquireTokenInteractive` foi `AcquireTokenSilent` adquirido através da chamada para ou devem ser visíveis na caixa **Token Info.** Os resultados contêm as seguintes propriedades:

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|
|**Nome de utilizador** |<span>user@domain.com</span> |O nome de utilizador utilizado para identificar o utilizador.|
|**Token Expira** |DateTime |O tempo em que o símbolo expira. A MSAL prolonga a data de validade renovando o símbolo conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer que o *utilizador.leia* o âmbito para ler o perfil de um utilizador. Este âmbito é automaticamente adicionado por padrão em todas as aplicações registadas no Portal de Registo de Aplicações. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor back-end, podem requerer âmbitos adicionais. A Microsoft Graph API requer os *Calendários.Leia* o âmbito para listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione os *Calendários.Leia* a permissão delegada à informação de registo de aplicações. Em seguida, adicione os *Calendários.Leia* o âmbito da `acquireTokenSilent` chamada.

>[!NOTE]
>O utilizador pode ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
