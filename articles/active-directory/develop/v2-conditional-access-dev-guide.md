---
title: Orientação do desenvolvedor para acesso condicional do Diretório Ativo Azure
titleSuffix: Microsoft identity platform
description: Orientação do desenvolvedor e cenários para acesso condicional AD Azure e plataforma de identidade microsoft.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: e4021f0ca2c1c9ca9434744a4aebb4b7938315f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584235"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Orientação do desenvolvedor para acesso condicional do Diretório Ativo Azure

A funcionalidade de Acesso Condicional no Azure Ative Directory (Azure AD) oferece uma das várias formas que pode usar para proteger a sua aplicação e proteger um serviço. O Acesso Condicional permite aos desenvolvedores e clientes empresariais proteger os serviços de várias formas, incluindo:

* [Autenticação multifator](../authentication/concept-mfa-howitworks.md)
* Permitir apenas dispositivos inscritos intune para aceder a serviços específicos
* Restringir as localizações dos utilizadores e as gamas IP

Para obter mais informações sobre as capacidades completas de Acesso Condicional, consulte o artigo [O que é acesso condicional.](../conditional-access/overview.md)

Para desenvolvedores que couem apps para Azure AD, este artigo mostra como você pode usar o Acesso Condicional e você também vai aprender sobre o impacto de aceder a recursos que você não tem controle sobre que pode ter políticas de Acesso Condicional aplicadas. O artigo também explora as implicações do Acesso Condicional no fluxo, aplicações web, acesso ao Microsoft Graph e APIs.

É assumido o conhecimento de aplicações [individuais](quickstart-register-app.md) e [multi-arrendantes](howto-convert-app-to-be-multi-tenant.md) e [padrões de autenticação comuns.](./authentication-vs-authorization.md)

> [!NOTE]
> A utilização desta funcionalidade requer uma licença Azure AD Premium P1. Para encontrar a licença certa para os seus requisitos, veja [Comparação das funcionalidades disponíveis geralmente das edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).
> Os clientes com [licenças de negócios microsoft 365](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a funcionalidades de Acesso Condicional.

## <a name="how-does-conditional-access-impact-an-app"></a>Como é que o Acesso Condicional afeta uma aplicação?

### <a name="app-types-impacted"></a>Tipos de aplicativos impactados

Na maioria dos casos comuns, o Acesso Condicional não altera o comportamento de uma aplicação nem requer quaisquer alterações do desenvolvedor. Apenas em certos casos quando uma aplicação solicita, indiretamente ou silenciosamente, um símbolo para um serviço, uma aplicação requer alterações de código para lidar com os desafios do Acesso Condicional. Pode ser tão simples como realizar um pedido de inscrição interativo.

Especificamente, os seguintes cenários requerem código para lidar com desafios de acesso condicional:

* Aplicativos que executam o fluxo em nome
* Aplicações que acedem a múltiplos serviços/recursos
* Aplicativos de uma só página que usam MSAL.js
* Web Apps chamando um recurso

As políticas de Acesso Condicional podem ser aplicadas à aplicação, mas também podem ser aplicadas a uma API web que a sua aplicação acede. Para saber mais sobre como configurar uma política de acesso condicional, consulte [Quickstart: Require MFA para aplicações específicas com acesso condicional do Diretório Ativo Azure](../authentication/tutorial-enable-azure-mfa.md).

Dependendo do cenário, um cliente da empresa pode aplicar e remover políticas de Acesso Condicional a qualquer momento. Para que a sua aplicação continue a funcionar quando uma nova política é aplicada, implemente o tratamento de desafios. Os exemplos que se seguem ilustram o tratamento do desafio.

### <a name="conditional-access-examples"></a>Exemplos de acesso condicional

Alguns cenários requerem alterações de código para lidar com o Acesso Condicional, enquanto outros funcionam como está. Aqui estão alguns cenários que usam o Acesso Condicional para fazer a autenticação de vários fatores que dá algumas informações sobre a diferença.

* Você está construindo uma aplicação iOS de um único inquilino e aplica uma política de acesso condicional. A aplicação assina num utilizador e não solicita acesso a uma API. Quando o utilizador entra, a política é automaticamente invocada e o utilizador precisa de realizar a autenticação multi-factor (MFA).
* Você está construindo uma aplicação nativa que usa um serviço de nível médio para aceder a uma API a jusante. Um cliente empresarial da empresa que utiliza esta aplicação aplica uma política à API a jusante. Quando um utilizador final entra, a aplicação nativa solicita o acesso ao nível médio e envia o token. O nível médio realiza em nome do fluxo para solicitar o acesso à API a jusante. Neste momento, é apresentado um "desafio" às reivindicações ao escalão intermédio. O nível médio envia o desafio de volta para a app nativa, que precisa de cumprir a política de Acesso Condicional.

#### <a name="microsoft-graph"></a>Microsoft Graph

O Microsoft Graph tem considerações especiais ao construir aplicações em ambientes de acesso condicionado. Geralmente, a mecânica do Acesso Condicional comporta-se da mesma forma, mas as políticas que os seus utilizadores vêem serão baseadas nos dados subjacentes que a sua aplicação está a solicitar a partir do gráfico.

Especificamente, todos os âmbitos do Microsoft Graph representam alguns conjuntos de dados que podem individualmente ter políticas aplicadas. Uma vez que as políticas de Acesso Condicional são atribuídas aos conjuntos de dados específicos, o Azure AD aplicará políticas de Acesso Condicional com base nos dados por trás do Gráfico - em vez do próprio Gráfico.

Por exemplo, se uma aplicação solicitar os seguintes âmbitos do Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Uma aplicação pode esperar que os seus utilizadores cumpram todas as políticas definidas em Reservas e Troca. Alguns âmbitos podem mapear vários conjuntos de dados se conceder acesso.

### <a name="complying-with-a-conditional-access-policy"></a>Cumprimento de uma política de acesso condicional

Para várias topologias de aplicações diferentes, uma política de acesso condicional é avaliada quando a sessão é estabelecida. Como uma política de Acesso Condicional opera na granularidade de apps e serviços, o ponto em que é invocado depende muito do cenário que está a tentar realizar.

Quando a sua aplicação tenta aceder a um serviço com uma política de Acesso Condicional, poderá encontrar um desafio de Acesso Condicional. Este desafio está codificado no `claims` parâmetro que vem numa resposta do Azure AD. Aqui está um exemplo deste parâmetro de desafio:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem aceitar este desafio e adiciá-lo a um novo pedido para Azure AD. A aprovação deste estado leva o utilizador final a executar todas as medidas necessárias para cumprir a política de Acesso Condicional. Nos seguintes cenários, explicam-se as especificidades do erro e como extrair o parâmetro.

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

O Azure AD Conditional Access é uma funcionalidade incluída no [Azure AD Premium.](../fundamentals/active-directory-whatis.md) Os clientes com [licenças de negócios microsoft 365](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a funcionalidades de Acesso Condicional.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações só se aplicam nestes cenários de Acesso Condicional:

* Aplicativos que executam o fluxo em nome
* Aplicações que acedem a múltiplos serviços/recursos
* Aplicativos de uma só página que usam MSAL.js

As secções seguintes discutem cenários comuns que são mais complexos. O princípio de funcionamento principal é que as políticas de acesso condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: App que executa o fluxo em nome do fluxo

Neste cenário, percorremos o caso em que uma aplicação nativa chama um serviço web/API. Por sua vez, este serviço faz o fluxo "em nome de" para chamar um serviço a jusante. No nosso caso, aplicámos a nossa política de Acesso Condicional ao serviço a jusante (Web API 2) e estamos a usar uma aplicação nativa em vez de uma aplicação de servidor/daemon.

![App executando o diagrama de fluxo em nome do fluxo](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

O pedido inicial de token para Web API 1 não leva o utilizador final para a autenticação de vários fatores, uma vez que a Web API 1 pode nem sempre atingir a API a jusante. Uma vez que a Web API 1 tenta solicitar um token em nome do utilizador para a Web API 2, o pedido falha uma vez que o utilizador não assinou com a autenticação de vários fatores.

A Azure AD devolve uma resposta HTTP com alguns dados interessantes:

> [!NOTE]
> Neste caso, é uma descrição de erro de autenticação multi-factor, mas há uma grande variedade de `interaction_required` possíveis relacionadas com o Acesso Condicional.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Na Web API 1, apanhamos o erro `error=interaction_required` e enviamos de volta o `claims` desafio para a aplicação de ambiente de trabalho. Nessa altura, a aplicação de ambiente de trabalho pode fazer uma nova `acquireToken()` chamada e anexar o `claims` desafio como um parâmetro de cadeia de consulta extra. Este novo pedido requer que o utilizador faça a autenticação multi-factor e, em seguida, envie este novo token de volta para a Web API 1 e complete o fluxo em nome do fluxo.

Para experimentar este cenário, consulte a nossa [amostra de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-). Demonstra como passar o desafio de reclamações de volta da Web API 1 para a app nativa e construir um novo pedido dentro da app do cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: App que acede a vários serviços

Neste cenário, percorremos o caso em que uma aplicação web acede a dois serviços um dos quais tem uma política de Acesso Condicional atribuída. Dependendo da lógica da sua aplicação, pode existir um caminho em que a sua aplicação não exija acesso a ambos os serviços web. Neste cenário, a ordem em que solicita um token desempenha um papel importante na experiência do utilizador final.

Vamos assumir que temos o serviço web A e B e o serviço web B tem a nossa política de Acesso Condicional aplicada. Embora o pedido inicial de auth interativo exija o consentimento para ambos os serviços, a política de acesso condicional não é necessária em todos os casos. Se a aplicação solicitar um token para o serviço web B, então a política é invocada e os pedidos subsequentes para o serviço web A também são bem sucedidos da seguinte forma.

![App que acede ao diagrama de fluxo de múltiplos serviços](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Em alternativa, se a aplicação solicitar inicialmente um token para o serviço web A, o utilizador final não invoca a política de Acesso Condicional. Isto permite ao desenvolvedor de aplicações controlar a experiência do utilizador final e não forçar a política de Acesso Condicional a ser invocada em todos os casos. O caso complicado é se a aplicação posteriormente solicitar um token para o serviço web B. Neste momento, o utilizador final tem de cumprir a política de Acesso Condicional. Quando a aplicação `acquireToken` tentar, pode gerar o seguinte erro (ilustrado no diagrama seguinte):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App que acede a vários serviços solicitando um novo token](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se a aplicação estiver a utilizar a biblioteca MSAL, a falha na aquisição do token é sempre novamente experimentada interativamente. Quando este pedido interativo ocorre, o utilizador final tem a oportunidade de cumprir o Acesso Condicional. Isto é verdade a menos que o pedido seja um `AcquireTokenSilentAsync` ou em que a app precisa realizar um pedido interativo para dar ao utilizador final a oportunidade `PromptBehavior.Never` de cumprir a ```AcquireToken``` política.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Cenário: Aplicação de página única (SPA) utilizando MSAL.js

Neste cenário, percorremos o caso quando temos uma aplicação de uma página única (SPA), utilizando MSAL.js para chamar uma API protegida de Acesso Condicional. Trata-se de uma arquitetura simples, mas tem algumas nuances que precisam de ser tidas em conta no desenvolvimento em torno do Acesso Condicional.

Em MSAL.js, há algumas funções que obtêm fichas: `loginPopup()` `acquireTokenSilent(...)` , e `acquireTokenPopup(…)` `acquireTokenRedirect(…)` .

* `loginPopup()` obtém um token de ID através de um pedido de inscrição interativa, mas não obtém fichas de acesso para qualquer serviço (incluindo uma API web protegida de acesso condicional).
* `acquireTokenSilent(…)` pode então ser usado para obter silenciosamente um token de acesso, o que significa que não mostra UI em nenhuma circunstância.
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` ambos são usados para solicitar interativamente um símbolo para um recurso que significa que eles sempre mostram uI de inscrição.

Quando uma aplicação precisa de um token de acesso para chamar uma API web, tenta um `acquireTokenSilent(…)` . Se a sessão de token expirar ou precisarmos de cumprir uma política de Acesso Condicional, então a função *acquireToken* falha e a aplicação utiliza `acquireTokenPopup()` ou `acquireTokenRedirect()` .

![App de uma página única usando diagrama de fluxo MSAL](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Vamos percorrer um exemplo com o nosso cenário de Acesso Condicional. O utilizador final aterrou no site e não tem sessão. Fazemos uma `loginPopup()` chamada, recebemos um token de identificação sem autenticação multi-factor. Em seguida, o utilizador toca num botão que requer que a app solicite dados a partir de uma API web. A aplicação tenta fazer uma `acquireTokenSilent()` chamada mas falha uma vez que o utilizador ainda não realizou a autenticação de vários fatores e precisa de cumprir a política de Acesso Condicional.

A Azure AD envia de volta a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

A nossa aplicação precisa de apanhar o `error=interaction_required` . A aplicação pode então utilizar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O utilizador é obrigado a fazer uma autenticação multi-factor. Depois de o utilizador completar a autenticação multi-factor, a aplicação é emitida um novo token de acesso para o recurso solicitado.

Para experimentar este cenário, consulte a nossa [amostra de código JS SPA Em nome de código.](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md) Esta amostra de código utiliza a política de Acesso Condicional e a API web que registou anteriormente com um JS SPA para demonstrar este cenário. Mostra como lidar corretamente com o desafio de reclamações e obter um token de acesso que pode ser usado para a sua API web. Alternativamente, check-out a amostra de [ código geralAngular.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) para orientação num SPA angular

## <a name="see-also"></a>Ver também

* Para saber mais sobre as capacidades, consulte [o Acesso Condicional no Diretório Ativo Azure.](../conditional-access/overview.md)
* Para obter mais amostras de código AD Azure, consulte [as amostras](sample-v2-code.md).
* Para obter mais informações sobre os MSAL SDK's e aceda à documentação de referência, consulte a visão geral da [Biblioteca de Autenticação](msal-overview.md)da Microsoft .
* Para saber mais sobre cenários multi-inquilinos, consulte [como assinar nos utilizadores usando o padrão multi-inquilino.](howto-convert-app-to-be-multi-tenant.md)
* Saiba mais sobre [o acesso condicional e garantir o acesso a aplicações IoT.](/azure/architecture/example-scenario/iot-aad/iot-aad)