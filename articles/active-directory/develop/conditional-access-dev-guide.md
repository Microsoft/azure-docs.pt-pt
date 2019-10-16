---
title: Diretrizes para desenvolvedores para Azure Active Directory acesso condicional
description: Diretrizes para desenvolvedores e cenários para acesso condicional do Azure AD
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91947c243b521e970a89152f76abe9a99142b89d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374011"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Diretrizes para desenvolvedores para Azure Active Directory acesso condicional

O recurso de acesso condicional no Azure Active Directory (AD do Azure) oferece uma das várias maneiras que você pode usar para proteger seu aplicativo e proteger um serviço. O acesso condicional permite que os desenvolvedores e clientes corporativos protejam os serviços de várias maneiras, incluindo:

* Autenticação multifator
* Permitindo que somente dispositivos registrados no Intune acessem serviços específicos
* Restringindo locais de usuário e intervalos de IP

Para obter mais informações sobre os recursos completos de acesso condicional, consulte [acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Para desenvolvedores que criam aplicativos para o Azure AD, este artigo mostra como você pode usar o acesso condicional e também aprenderá sobre o impacto de acessar os recursos que você não tem controle sobre isso pode ter políticas de acesso condicional aplicadas. O artigo também explora as implicações de acesso condicional no fluxo em nome de, aplicativos Web, acessando Microsoft Graph e chamando APIs.

O conhecimento de aplicativos [únicos](quickstart-v1-integrate-apps-with-azure-ad.md) e [multilocatários](howto-convert-app-to-be-multi-tenant.md) e [padrões de autenticação comuns](v1-authentication-scenarios.md) é assumido.

## <a name="how-does-conditional-access-impact-an-app"></a>Como o acesso condicional afeta um aplicativo?

### <a name="app-types-impacted"></a>Tipos de aplicativo afetados

Na maioria dos casos comuns, o acesso condicional não altera o comportamento de um aplicativo ou requer qualquer alteração do desenvolvedor. Somente em determinados casos em que um aplicativo solicita indiretamente ou silenciosamente um token para um serviço, um aplicativo requer alterações de código para lidar com "desafios" de acesso condicional. Pode ser tão simples quanto executar uma solicitação de entrada interativa.

Especificamente, os cenários a seguir exigem código para lidar com "desafios" de acesso condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando ADAL. js
* Aplicativos Web chamando um recurso

As políticas de acesso condicional podem ser aplicadas ao aplicativo, mas também podem ser aplicadas a uma API da Web acessada por seu aplicativo. Para saber mais sobre como configurar uma política de acesso condicional, consulte [início rápido: exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](../conditional-access/app-based-mfa.md).

Dependendo do cenário, um cliente corporativo pode aplicar e remover políticas de acesso condicional a qualquer momento. Para que seu aplicativo continue funcionando quando uma nova política for aplicada, você precisará implementar a manipulação de "desafio". Os exemplos a seguir ilustram o tratamento de desafio.

### <a name="conditional-access-examples"></a>Exemplos de acesso condicional

Alguns cenários exigem alterações de código para lidar com o acesso condicional, enquanto outros funcionam como estão. Aqui estão alguns cenários que usam o acesso condicional para fazer a autenticação multifator que oferece uma visão da diferença.

* Você está criando um aplicativo iOS de locatário único e aplica uma política de acesso condicional. O aplicativo assina um usuário e não solicita acesso a uma API. Quando o usuário faz logon, a política é invocada automaticamente e o usuário precisa executar a autenticação multifator (MFA). 
* Você está criando um aplicativo nativo que usa um serviço de camada intermediária para acessar uma API downstream. Um cliente corporativo na empresa que usa esse aplicativo aplica uma política à API downstream. Quando um usuário final entra, o aplicativo nativo solicita acesso à camada intermediária e envia o token. A camada intermediária executa o fluxo em nome de para solicitar acesso à API downstream. Neste ponto, um "desafio" de declarações é apresentado à camada intermediária. A camada intermediária envia o desafio de volta para o aplicativo nativo, que precisa estar em conformidade com a política de acesso condicional.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph tem considerações especiais ao criar aplicativos em ambientes de acesso condicional. Em geral, a mecânica do acesso condicional se comporta da mesma forma, mas as políticas que os usuários veem serão baseadas nos dados subjacentes que seu aplicativo está solicitando do grafo. 

Especificamente, todos os escopos de Microsoft Graph representam alguns conjuntos de conjunto que podem ter políticas aplicadas individualmente. Como as políticas de acesso condicional são atribuídas aos conjuntos de dados específicos, o AD do Azure impedirá políticas de acesso condicional com base em gráficos por trás do grafo, em vez do próprio grafo.

Por exemplo, se um aplicativo solicitar os seguintes escopos de Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Um aplicativo pode esperar que seus usuários atendam a todas as políticas definidas em livros e no Exchange. Alguns escopos podem ser mapeados para vários conjuntos de valores se ele conceder acesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Conformidade com uma política de acesso condicional

Para várias topologias de aplicativo diferentes, uma política de acesso condicional é avaliada quando a sessão é estabelecida. Como uma política de acesso condicional opera na granularidade de aplicativos e serviços, o ponto em que ele é invocado depende muito do cenário que você está tentando realizar.

Quando seu aplicativo tenta acessar um serviço com uma política de acesso condicional, ele pode encontrar um desafio de acesso condicional. Esse desafio é codificado no parâmetro `claims` que vem em uma resposta do Azure AD. Aqui está um exemplo desse parâmetro de desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem pegar esse desafio e acrescentá-lo a uma nova solicitação ao Azure AD. Passar esse estado solicita que o usuário final execute qualquer ação necessária para obedecer à política de acesso condicional. Nos cenários a seguir, são explicadas as especificações do erro e como extrair o parâmetro.

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

O acesso condicional do Azure AD é um recurso incluído no [Azure ad Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Você pode saber mais sobre os requisitos de licenciamento no [relatório uso não licenciado](../active-directory-conditional-access-unlicensed-usage-report.md). Os desenvolvedores podem ingressar na [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), que inclui uma assinatura gratuita do Enterprise Mobility Suite, que inclui Azure ad Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As informações a seguir se aplicam somente a esses cenários de acesso condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando ADAL. js

As seções a seguir discutem cenários comuns que são mais complexos. O princípio de operação principal é que as políticas de acesso condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: aplicativo executando o fluxo em nome de

Nesse cenário, percorremos o caso em que um aplicativo nativo chama um serviço Web/API. Por sua vez, esse serviço faz o fluxo "em nome de" para chamar um serviço downstream. Em nosso caso, aplicamos nossa política de acesso condicional ao serviço downstream (API Web 2) e estamos usando um aplicativo nativo em vez de um aplicativo de servidor/daemon. 

![Aplicativo executando o diagrama de fluxo em nome de](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

A solicitação de token inicial para a API Web 1 não solicita ao usuário final a autenticação multifator, já que a API da Web 1 nem sempre pode atingir a API downstream. Depois que a API da Web 1 tentar solicitar um token em nome do usuário para a API Web 2, a solicitação falhará, pois o usuário não entrou com a autenticação multifator.

O Azure AD retorna uma resposta HTTP com alguns dados interessantes:

> [!NOTE]
> Nessa instância, é uma descrição de erro de autenticação multifator, mas há uma grande variedade de `interaction_required` possível pertencente ao acesso condicional.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Na API da Web 1, capturamos o erro `error=interaction_required` e enviamos de volta o desafio de `claims` para o aplicativo da área de trabalho. Nesse ponto, o aplicativo de desktop pode fazer uma nova chamada `acquireToken()` e acrescentar o `claims`challenge como um parâmetro de cadeia de caracteres de consulta extra. Essa nova solicitação exige que o usuário faça a autenticação multifator e envie esse novo token de volta para a API da Web 1 e conclua o fluxo em nome de.

Para testar esse cenário, consulte nosso [exemplo de código .net](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ele demonstra como passar o desafio de declarações de volta da API Web 1 para o aplicativo nativo e construir uma nova solicitação dentro do aplicativo cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: aplicativo acessando vários serviços

Nesse cenário, percorremos o caso em que um aplicativo Web acessa dois serviços, um dos quais tem uma política de acesso condicional atribuída. Dependendo da lógica do aplicativo, pode existir um caminho no qual seu aplicativo não precisa acessar os dois serviços Web. Nesse cenário, a ordem na qual você solicita um token desempenha um papel importante na experiência do usuário final.

Vamos supor que o serviço Web A e B e o serviço Web B tenham nossa política de acesso condicional aplicada. Embora a solicitação de autenticação interativa inicial exija consentimento para ambos os serviços, a política de acesso condicional não é necessária em todos os casos. Se o aplicativo solicitar um token para o serviço Web B, a política será invocada e as solicitações subsequentes para o serviço Web A também serão bem sucedidos da seguinte maneira.

![Diagrama de fluxo de acesso a vários serviços do aplicativo](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Como alternativa, se o aplicativo solicitar inicialmente um token para o serviço Web a, o usuário final não invocará a política de acesso condicional. Isso permite que o desenvolvedor do aplicativo controle a experiência do usuário final e não force a política de acesso condicional a ser invocada em todos os casos. O caso complicado é que, em seguida, o aplicativo solicita um token para o serviço Web B. Neste ponto, o usuário final precisa estar em conformidade com a política de acesso condicional. Quando o aplicativo tenta `acquireToken`, ele pode gerar o seguinte erro (ilustrado no diagrama a seguir):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplicativo acessando vários serviços solicitando um novo token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se o aplicativo estiver usando a biblioteca ADAL, uma falha ao adquirir o token será sempre repetida interativamente. Quando essa solicitação interativa ocorre, o usuário final tem a oportunidade de cumprir o acesso condicional. Isso é verdadeiro, a menos que a solicitação seja um `AcquireTokenSilentAsync` ou `PromptBehavior.Never`, nesse caso, o aplicativo precisa executar uma solicitação ```AcquireToken``` interativa para dar ao usuário final a oportunidade de cumprir a política.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Cenário: aplicativo de página única (SPA) usando ADAL. js

Nesse cenário, percorremos o caso em que temos um aplicativo de página única (SPA), usando o ADAL. js para chamar uma API Web protegida por acesso condicional. Essa é uma arquitetura simples, mas tem algumas nuances que precisam ser levadas em conta ao desenvolver em volta do acesso condicional.

No ADAL. js, há algumas funções que obtêm tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `login()` Obtém um token de ID por meio de uma solicitação de entrada interativa, mas não obtém tokens de acesso para nenhum serviço (incluindo uma API Web protegida por acesso condicional).
* o `acquireToken(…)` pode então ser usado para obter silenciosamente um token de acesso, o que significa que ele não mostra a interface do usuário em nenhuma circunstância.
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` são usados para solicitar interativamente um token para um recurso, o que significa que eles sempre mostram a interface do usuário de entrada.

Quando um aplicativo precisa de um token de acesso para chamar uma API da Web, ele tenta um `acquireToken(…)`. Se a sessão de token estiver expirada ou precisar estar em conformidade com uma política de acesso condicional, a função *acquireToken* falhará e o aplicativo usará `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Aplicativo de página única usando o diagrama de fluxo ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Vamos examinar um exemplo com nosso cenário de acesso condicional. O usuário final apenas descarregou no site e não tem uma sessão. Realizamos uma chamada `login()`, obtenho um token de ID sem autenticação multifator. Em seguida, o usuário acessa um botão que exige que o aplicativo solicite dados de uma API da Web. O aplicativo tenta fazer uma chamada `acquireToken()`, mas falha, pois o usuário ainda não executou a autenticação multifator e precisa estar em conformidade com a política de acesso condicional.

O Azure AD envia de volta a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nosso aplicativo precisa capturar o `error=interaction_required`. Em seguida, o aplicativo pode usar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O usuário é forçado a fazer uma autenticação multifator. Depois que o usuário concluir a autenticação multifator, o aplicativo receberá um novo token de acesso para o recurso solicitado.

Para testar esse cenário, consulte nosso [exemplo de código em nome de spa do js](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Este exemplo de código usa a política de acesso condicional e a API da Web que você registrou anteriormente com um SPA do JS para demonstrar esse cenário. Ele mostra como lidar corretamente com o desafio de declarações e obter um token de acesso que pode ser usado para sua API Web. Como alternativa, faça o check-out do [exemplo de código angular. js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) geral para obter orientação sobre um spa angular

## <a name="see-also"></a>Ver também

* Para saber mais sobre os recursos, consulte [acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Para obter mais exemplos de código do Azure AD, consulte [repositório GitHub de exemplos de código](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Para obter mais informações sobre o SDK do ADAL e acessar a documentação de referência, consulte [Guia de biblioteca](active-directory-authentication-libraries.md).
* Para saber mais sobre cenários de multilocatário, confira [como conectar usuários usando o padrão multilocatário](howto-convert-app-to-be-multi-tenant.md).
