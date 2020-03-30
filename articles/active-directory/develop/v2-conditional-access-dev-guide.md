---
title: Orientação do desenvolvedor para acesso condicional do Diretório Ativo Azure
description: Orientação e cenários para o Azure AD Conditional Access e a plataforma de identidade da Microsoft.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 03/16/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d82840681450ec855cb35c8700da2a53b9dd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481454"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Orientação do desenvolvedor para acesso condicional do Diretório Ativo Azure

A funcionalidade de Acesso Condicional no Azure Ative Directory (Azure AD) oferece uma das várias formas que pode utilizar para proteger a sua app e proteger um serviço. O Acesso Condicional permite aos desenvolvedores e clientes empresariais proteger os serviços de várias formas, incluindo:

* Multi-Factor Authentication
* Permitindo que apenas dispositivos inscritos Intune acedam a serviços específicos
* Restringir a localização dos utilizadores e as gamas IP

Para obter mais informações sobre as capacidades completas de Acesso Condicional, consulte [acesso condicional no Diretório Ativo Azure](../active-directory-conditional-access-azure-portal.md).

Para os desenvolvedores que criam aplicativos para o Azure AD, este artigo mostra como pode usar o Acesso Condicional e também aprenderá sobre o impacto do acesso a recursos que não tem controlo sobre o que pode ter políticas de Acesso Condicional aplicadas. O artigo também explora as implicações do Acesso Condicional no fluxo em nome do fluxo, aplicações web, acesso ao Microsoft Graph e chamadas APIs.

É assumido o conhecimento de aplicações [individuais](quickstart-register-app.md) e [multi-inquilinos](howto-convert-app-to-be-multi-tenant.md) e [padrões comuns de autenticação.](authentication-scenarios.md)

> [!NOTE]
> A utilização desta funcionalidade requer uma licença Azure AD Premium P1. Para encontrar a licença certa para os seus requisitos, veja [Comparação das funcionalidades disponíveis geralmente das edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).
> Os clientes com [licenças empresariais Microsoft 365](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso às funcionalidades de Acesso Condicional.

## <a name="how-does-conditional-access-impact-an-app"></a>Como é que o Acesso Condicional afeta uma aplicação?

### <a name="app-types-impacted"></a>Tipos de aplicativos impactados

Na maioria dos casos comuns, o Acesso Condicional não altera o comportamento de uma aplicação nem requer quaisquer alterações por parte do desenvolvedor.Só em certos casos quando uma aplicação solicita indiretamente ou silenciosamente um símbolo para um serviço, uma aplicação requer alterações de código para lidar com "desafios" de Acesso Condicional.Pode ser tão simples como realizar um pedido de inscrição interativo.

Especificamente, os seguintes cenários requerem código para lidar com "desafios" de Acesso Condicional:

* Aplicativos que executam o fluxo em nome
* Aplicativos que acedem a vários serviços/recursos
* Aplicativos de página única usando MSAL.js
* Web Apps chamando um recurso

As políticas de Acesso Condicional podem ser aplicadas à aplicação, mas também podem ser aplicadas a uma API web a que a sua aplicação aceda. Para saber mais sobre como configurar uma política de acesso condicional, consulte [Quickstart: Require MFA para aplicações específicas com acesso condicional de diretório ativo Azure](../conditional-access/app-based-mfa.md).

Dependendo do cenário, um cliente empresarial pode aplicar e remover as políticas de Acesso Condicional a qualquer momento. Para que a sua aplicação continue a funcionar quando uma nova política é aplicada, é necessário implementar o manuseamento do "desafio". Os exemplos que se seguem ilustram o tratamento do desafio.

### <a name="conditional-access-examples"></a>Exemplos de Acesso Condicional

Alguns cenários requerem alterações de código para lidar com o Acesso Condicional, enquanto outros funcionam como está. Aqui estão alguns cenários usando o Acesso Condicional para fazer a autenticação de vários fatores que dá alguma visão sobre a diferença.

* Está a construir uma aplicação iOS de um único inquilino e aplica uma política de Acesso Condicional. A aplicação assina num utilizador e não solicita acesso a uma API. Quando o utilizador faz o sinal de insígnio, a política é automaticamente invocada e o utilizador precisa de realizar a autenticação de vários fatores (MFA). 
* Você está construindo uma aplicação nativa que usa um serviço de nível médio para aceder a uma API a jusante. Um cliente empresarial da empresa que utiliza esta aplicação aplica uma política à API a jusante. Quando um utilizador final faz o sinal, a aplicação nativa pede acesso ao nível médio e envia o símbolo. O nível médio executa em nome do fluxo para solicitar o acesso à API a jusante. Neste momento, é apresentado um "desafio" ao nível médio. O nível médio envia o desafio de volta para a app nativa, que precisa de cumprir com a política de Acesso Condicional.

#### <a name="microsoft-graph"></a>Microsoft Graph

O Microsoft Graph tem considerações especiais na construção de aplicações em ambientes de Acesso Condicional. Geralmente, a mecânica do Acesso Condicional comporta-se da mesma forma, mas as políticas que os seus utilizadores vêem serão baseadas nos dados subjacentes que a sua aplicação está a solicitar a partir do gráfico. 

Especificamente, todos os âmbitos do Microsoft Graph representam algum conjunto de dados que pode individualmente ter políticas aplicadas. Uma vez que as políticas de Acesso Condicional são atribuídas aos conjuntos de dados específicos, a Azure AD aplicará políticas de Acesso Condicional com base nos dados por trás do Graph - em vez do próprio Graph.

Por exemplo, se uma aplicação solicitar os seguintes âmbitos do Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Uma aplicação pode esperar que os seus utilizadores cumpram todas as políticas definidas nas Reservas e Trocas. Alguns âmbitos podem mapear vários conjuntos de dados se conceder acesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Cumprimento de uma política de acesso condicional

Para várias topoologias de aplicações diferentes, uma política de Acesso Condicional é avaliada quando a sessão é estabelecida. Como uma política de Acesso Condicional funciona na granularidade de apps e serviços, o ponto em que é invocado depende fortemente do cenário que está a tentar realizar.

Quando a sua aplicação tenta aceder a um serviço com uma política de Acesso Condicional, pode encontrar um desafio de Acesso Condicional. Este desafio está codificado `claims` no parâmetro que vem numa resposta da Azure AD. Aqui está um exemplo deste parâmetro de desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem aceitar este desafio e apenhá-lo num novo pedido ao Azure AD. A aprovação deste estado leva o utilizador final a executar todas as medidas necessárias para cumprir a política de Acesso Condicional. Nos seguintes cenários, explicam-se as especificidades do erro e a forma de extrair o parâmetro.

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

O Azure AD Conditional Access é uma funcionalidade incluída no [Azure AD Premium.](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) Os clientes com [licenças empresariais Microsoft 365](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso às funcionalidades de Acesso Condicional.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações aplicam-se apenas nestes cenários de Acesso Condicional:

* Aplicativos que executam o fluxo em nome
* Aplicativos que acedem a vários serviços/recursos
* Aplicativos de página única usando MSAL.js

As seguintes secções discutem cenários comuns mais complexos. O princípio de funcionamento fundamental é que as políticas de Acesso Condicional são avaliadas no momento em que o símbolo é solicitado para o serviço que tem uma política de Acesso Condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: App que executa o fluxo em nome

Neste cenário, percorremos o caso em que uma aplicação nativa chama um serviço web/API. Por sua vez, este serviço faz o fluxo "em nome" para chamar um serviço a jusante. No nosso caso, aplicámos a nossa política de Acesso Condicional ao serviço a jusante (Web API 2) e estamos a usar uma aplicação nativa em vez de uma aplicação servidor/daemon. 

![App executando o diagrama de fluxo em nome](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

O pedido inicial de ficha para Web API 1 não solicita ao utilizador final a autenticação de vários fatores, uma vez que a Web API 1 nem sempre pode atingir a API a jusante. Uma vez que a Web API 1 tenta solicitar um token em nome do utilizador para Web API 2, o pedido falha uma vez que o utilizador não assinou com a autenticação de vários fatores.

A Azure AD devolve uma resposta HTTP com alguns dados interessantes:

> [!NOTE]
> Neste caso, é uma descrição de erro de autenticação multifactor, mas há uma ampla gama de `interaction_required` possíveis relativas ao Acesso Condicional.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Na Web API 1, `error=interaction_required`apanhamos o `claims` erro e enviamos de volta o desafio para a aplicação de ambiente de trabalho. Nessa altura, a aplicação de `acquireToken()` desktop pode `claims`fazer uma nova chamada e anexar o desafio como um parâmetro de corda extra de consulta. Este novo pedido requer que o utilizador faça a autenticação de vários fatores e, em seguida, envie este novo token de volta para a Web API 1 e complete o fluxo em nome do fluxo.

Para experimentar este cenário, consulte a nossa [amostra de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Demonstra como passar o desafio de reclamações de volta da Web API 1 para a aplicação nativa e construir um novo pedido dentro da app do cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: App que acede a vários serviços

Neste cenário, percorremos o caso em que uma aplicação web acede a dois serviços, um dos quais tem uma política de Acesso Condicional atribuída. Dependendo da lógica da sua aplicação, pode existir um caminho no qual a sua aplicação não requer acesso a ambos os serviços web. Neste cenário, a ordem em que solicita um símbolo desempenha um papel importante na experiência final do utilizador.

Vamos assumir que temos o serviço web A e B e o serviço web B tem a nossa política de Acesso Condicional aplicada. Embora o pedido inicial de auth interativo exija o consentimento para ambos os serviços, a política de Acesso Condicional não é necessária em todos os casos. Se a aplicação solicitar um símbolo para o serviço web B, então a política é invocada e os pedidos subsequentes de serviço web A também têm sucesso da seguinte forma.

![App que acede ao diagrama de fluxo de múltiplos serviços](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Em alternativa, se a aplicação solicitar inicialmente um símbolo para o serviço web A, o utilizador final não invoca a política de Acesso Condicional. Isto permite ao desenvolvedor de aplicações controlar a experiência do utilizador final e não forçar a invocidade da política de Acesso Condicional em todos os casos. O caso complicado é se a aplicação solicitar posteriormente um sinal para o serviço web B. Neste momento, o utilizador final tem de cumprir a política de Acesso Condicional. Quando a aplicação `acquireToken`tenta, pode gerar o seguinte erro (ilustrado no seguinte diagrama):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App que acede a vários serviços solicitando um novo símbolo](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se a aplicação estiver a utilizar a biblioteca MSAL, uma falha na aquisição do token é sempre novamente experimentada interativamente. Quando este pedido interativo ocorre, o utilizador final tem a oportunidade de cumprir o Acesso Condicional. Isto é verdade, a `AcquireTokenSilentAsync` `PromptBehavior.Never` menos que o pedido seja ```AcquireToken``` a ou nesse caso a app precisa realizar um pedido interativo para dar ao utilizador final a oportunidade de cumprir a apólice.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Cenário: Aplicação de página única (SPA) utilizando MSAL.js

Neste cenário, percorremos o caso quando temos uma aplicação de uma página única (SPA), usando MSAL.js para chamar uma API web protegida de Acesso Condicional. Esta é uma arquitetura simples, mas tem algumas nuances que precisam de ser tidas em conta no desenvolvimento em torno do Acesso Condicional.

Em MSAL.js, existem algumas funções que `loginPopup()` `acquireTokenSilent(...)`obtêm `acquireTokenPopup(…)`fichas: , , e `acquireTokenRedirect(…)`.

* `loginPopup()`obtém um token de id através de um pedido de inscrição interativo, mas não obtém fichas de acesso para qualquer serviço (incluindo uma API web protegida de acesso condicional).
* `acquireTokenSilent(…)`pode então ser usado para obter silenciosamente um sinal de acesso, o que significa que não mostra UI em nenhuma circunstância.
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são ambos usados para solicitar interativamente um símbolo para um recurso, o que significa que eles sempre mostram sign-in UI.

Quando uma aplicação precisa de um sinal de acesso `acquireTokenSilent(…)`para chamar uma API Web, tenta um . Se a sessão do token expirar ou tivermos de cumprir uma política de Acesso `acquireTokenPopup()` Condicional, então a função *token adquire* falha e a aplicação utiliza ou `acquireTokenRedirect()`.

![Aplicação de página única usando diagrama de fluxo MSAL](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Vamos dar um exemplo com o nosso cenário de Acesso Condicional. O utilizador final acabou de aterrar no site e não tem uma sessão. Fazemos uma `loginPopup()` chamada, recebemos um símbolo de identificação sem autenticação de vários fatores. Em seguida, o utilizador acerta num botão que requer que a app solicite dados de uma API web. A aplicação tenta `acquireTokenSilent()` fazer uma chamada mas falha uma vez que o utilizador ainda não realizou a autenticação de vários fatores e precisa de cumprir a política de Acesso Condicional.

A Azure AD remete a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

A nossa aplicação `error=interaction_required`precisa de apanhar o . A aplicação pode `acquireTokenPopup()` `acquireTokenRedirect()` então utilizar ou no mesmo recurso. O utilizador é obrigado a fazer uma autenticação multifactor. Após o utilizador completar a autenticação de vários fatores, a aplicação é emitida um novo sinal de acesso para o recurso solicitado.

Para experimentar este cenário, consulte a nossa [amostra de código JS SPA em nome de código](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Esta amostra de código utiliza a política de Acesso Condicional e a API web que registou anteriormente com um JS SPA para demonstrar este cenário. Mostra como lidar corretamente com o desafio das reclamações e obter um sinal de acesso que pode ser usado para a sua Web API. Alternativamente, check-out a amostra geral de [código Angular.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) para orientação em um SPA angular

## <a name="see-also"></a>Consulte também

* Para saber mais sobre as capacidades, consulte [Acesso Condicional no Diretório Ativo Azure.](/azure/active-directory/conditional-access/overview)
* Para mais amostras de código Azure AD, consulte [amostras](sample-v2-code.md).
* Para obter mais informações sobre os SDK's mSAL e aceder à documentação de referência, consulte a [visão geral da Microsoft Authentication Library](msal-overview.md).
* Para saber mais sobre cenários multi-inquilinos, consulte [como inscrever utilizadores usando o padrão multi-inquilino](howto-convert-app-to-be-multi-tenant.md).
* Saiba mais sobre [o acesso condicional e garantir o acesso a aplicações IoT.](/azure/architecture/example-scenario/iot-aad/iot-aad)
