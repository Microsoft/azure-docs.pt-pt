---
title: ficheiro de inclusão
description: ficheiro de inclusão
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181545"
---
## <a name="test-your-code"></a>Teste o seu código

Para executar o seu projeto, no Visual Studio, selecione **F5**. A sua aplicação **MainWindow** é apresentada, como mostrado aqui:

![Teste a sua aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

A primeira vez que executar a aplicação e selecionar o botão **API do Gráfico de Chamada** microsoft, é solicitado que faça o seu sôm. Utilize uma conta Azure Ative Directory (conta de trabalho ou escola) ou uma conta Microsoft (live.com, outlook.com) para testá-la.

![Iniciar sessão na aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que iniciar sessão na sua candidatura, também é solicitado que dê consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva, como mostrado aqui:

![Forneça o seu consentimento para o acesso à aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, deverá ver as informações do perfil do utilizador que são devolvidas pela chamada para a API do Gráfico da Microsoft. Os resultados são apresentados na caixa de Resultados de **Chamadas API.** Informações básicas sobre o token que foi adquirido através da chamada para `AcquireTokenInteractive` ou `AcquireTokenSilent` devem ser visíveis na caixa de **Info Token.** Os resultados contêm as seguintes propriedades:

|Propriedade  |Formato  |Description |
|---------|---------|---------|
|**Nome de utilizador** |<span>user@domain.com</span> |O nome de utilizador que é usado para identificar o utilizador.|
|**Token expira** |DateTime |O momento em que o símbolo expira. A MSAL prolonga a data de validade renovando o token se necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

A API do Microsoft Graph requer que o âmbito de leitura do *utilizador.leia* para ler o perfil de um utilizador. Este âmbito é automaticamente adicionado por padrão em todas as aplicações registadas no Portal de Registo de Candidaturas. Outros APIs para o Microsoft Graph, bem como APIs personalizados para o seu servidor back-end, podem necessitar de âmbitos adicionais. A API do Gráfico microsoft requer o *âmbito Calendários.Ler* para listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione os *Calendários.Leia* a permissão delegada na informação de registo de candidatura. Em seguida, adicione os *Calendários.Leia* o âmbito da `acquireTokenSilent` chamada.

>[!NOTE]
>O utilizador poderá ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
