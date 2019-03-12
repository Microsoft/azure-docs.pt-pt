---
title: Orientação para programadores de acesso condicional do Azure Active Directory
description: Cenários para acesso condicional do Azure AD e orientação para programadores
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
ms.author: celested
ms.reviewer: dadobali
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: c02f094def3828d0839025f4b7dea48ee64adcc8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543191"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Orientação para programadores de acesso condicional do Azure Active Directory

A funcionalidade de acesso condicional no Azure Active Directory (Azure AD) oferece uma das várias formas que pode utilizar para proteger a sua aplicação e proteger um serviço. Acesso condicional permite aos desenvolvedores e clientes empresariais proteger serviços numa infinidade de maneiras, incluindo:

* Multi-Factor Authentication
* Permitir que apenas o Intune inscrito os dispositivos para aceder a serviços específicos
* Intervalos de restringir os locais do usuário e de IP

Para obter mais informações sobre as funcionalidades completas do acesso condicional, consulte [acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Para os desenvolvedores a criação de aplicativos para o Azure AD, este artigo mostra como pode utilizar o acesso condicional e também aprenderá sobre o impacto do acesso a recursos que não tem controle sobre o que poderá ter de aplicar políticas de acesso condicional. O artigo também explora as implicações de acesso condicional no fluxo em-nome-de, aplicações web, aceder ao Microsoft Graph e chamar APIs.

Conhecimento dos [único](quickstart-v1-integrate-apps-with-azure-ad.md) e [multi-inquilino](howto-convert-app-to-be-multi-tenant.md) aplicações e [padrões comuns de autenticação](authentication-scenarios.md) pressupõe-se.

## <a name="how-does-conditional-access-impact-an-app"></a>Como é que o acesso condicional afetar uma aplicação?

### <a name="app-types-impacted"></a>O impacto de tipos de aplicações

Na maioria dos casos, o acesso condicional não altera o comportamento de uma aplicação ou requer que todas as alterações do desenvolvedor. Apenas em determinados casos quando uma aplicação indiretamente ou silenciosamente solicita um token para um serviço, uma aplicação necessitar de alterações de código para lidar com acesso condicional "desafios". Pode ser tão simples quanto executar um pedido de início de sessão interativo.

Os seguintes cenários requerem especificamente, o código para lidar com acesso condicional "desafios":

* Aplicações a executar o fluxo em-nome-de
* Aplicações acedam aos vários serviços/recursos
* Aplicações de página única com ADAL.js
* Chamar um recurso de aplicações Web

Acesso condicional, as políticas podem ser aplicadas à aplicação, mas também podem ser aplicadas a uma API web a sua aplicação acede. Para saber mais sobre como configurar uma política de acesso condicional, veja [início rápido: Exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](../conditional-access/app-based-mfa.md).

Dependendo do cenário, um cliente empresarial pode aplicar e remover as políticas de acesso condicional em qualquer altura. Para a sua aplicação continuar a funcionar quando uma nova política é aplicada, terá de implementar o tratamento de "desafio". Os exemplos seguintes mostram manipulação de desafio.

### <a name="conditional-access-examples"></a>Exemplos de acesso condicional

Alguns cenários requerem alterações de código para lidar com acesso condicional, ao passo que outras pessoas são compatíveis. Seguem-se alguns cenários, usando o acesso condicional para fazer a autenticação multifator que fornece algum esclarecimento sobre a diferença.

* Estão a criar uma aplicação iOS de inquilino único e aplicar uma política de acesso condicional. A aplicação inicia sessão um utilizador e não pedir o acesso a uma API. Quando o utilizador inicia sessão, a política é invocada automaticamente e o utilizador precisa de realizar a autenticação multifator (MFA). 
* Está criando um aplicativo nativo que usa um serviço de camada intermediária para aceder a uma API de downstream. Um cliente empresarial na empresa utilizar esta aplicação aplica-se uma política para a API de downstream. Quando um utilizador final inicia sessão, a aplicação nativa pede acesso para a camada intermediária e envia o token. A camada média executa em-nome-de fluxo para pedir acesso para a API de downstream. Neste momento, um desafio"afirmações" é apresentado para a camada intermediária. A camada média envia o desafio para a aplicação nativa, o que tem de estar em conformidade com a política de acesso condicional.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph tem considerações especiais ao construir aplicativos em ambientes de acesso condicional. Em geral, a mecânica do acesso condicional se comportam da mesma, mas as políticas, que veja os seus utilizadores irão basear-se nos dados subjacentes que a aplicação está a solicitar do gráfico. 

Especificamente, todos os âmbitos do Microsoft Graph representam um conjunto de dados individualmente pode ter políticas aplicadas. Uma vez que as políticas de acesso condicional são atribuídas os conjuntos de dados específicos, do Azure AD irá impor políticas de acesso condicional com base nos dados por trás do gráfico: em vez de criar um gráfico em si.

Por exemplo, se um aplicativo solicita os seguintes âmbitos do Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Uma aplicação pode esperar que seus usuários para atender a todas as diretivas definidas no Bookings e o Exchange. Alguns âmbitos podem mapear para vários conjuntos de dados, se ele concede acesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Conformidade com uma política de acesso condicional

Para várias topologias de aplicação diferente, uma política de acesso condicional é avaliada quando a sessão for estabelecida. Como uma política de acesso condicional funciona numa granularidade de aplicações e serviços, o ponto em que é invocado depende muito o cenário que está tentando realizar.

Quando a aplicação tenta acessar um serviço com uma política de acesso condicional, pode encontrar um desafio de acesso condicional. Esse desafio está codificado no `claims` parâmetro que é fornecido numa resposta do Azure AD. Eis um exemplo deste parâmetro desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem assumir esse desafio e acrescentá-lo num novo pedido para o Azure AD. Passando desse Estado solicita que o utilizador final para efetuar qualquer ação necessária para cumprir a política de acesso condicional. Nos cenários a seguir, são explicadas informações específicas sobre o erro e como extrair o parâmetro.

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

Acesso condicional do Azure AD é uma funcionalidade incluída no [do Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Pode saber mais sobre o licenciamento requisitos no [relatório de utilização não licenciados](../active-directory-conditional-access-unlicensed-usage-report.md). Os desenvolvedores podem participar da [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), que inclui uma subscrição gratuita para o Enterprise Mobility Suite, que inclui o Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações aplicam-se apenas nos seguintes cenários de acesso condicional:

* Aplicações a executar o fluxo em-nome-de
* Aplicações acedam aos vários serviços/recursos
* Aplicações de página única com ADAL.js

As secções seguintes abordam cenários comuns que são mais complexos. Centro operacional princípio é o acesso condicional, as políticas são avaliadas no momento que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: Executar o fluxo em-nome-de aplicação

Neste cenário, vamos analisar o caso em que uma aplicação nativa chama uma serviço/API web. Por sua vez, esse serviço faz [he "fluxo em-nome-de" para chamar um serviço downstream. No nosso caso, estamos aplicou a nossa política de acesso condicional para o serviço downstream (Web API 2) e estiver a utilizar uma aplicação nativa, em vez de uma aplicação de servidor/daemon. 

![Aplicação efetuar o diagrama de fluxo em-nome-de](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

O pedido de token inicial para a Web API 1 não solicita o utilizador final para multi-factor authentication como 1 de API Web não pode atingir sempre a API de downstream. Depois de tentar Web 1 da API solicitar um token em-nome-do utilizador para a Web API 2, o pedido falhar, uma vez que o utilizador não iniciou sessão com a autenticação multifator.

O Azure AD retorna uma resposta HTTP com alguns dados interessantes:

> [!NOTE]
> Neste caso é uma descrição de erro de autenticação multifator, mas há uma grande variedade de `interaction_required` possível relativas ao acesso condicional.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Em 1 de API da Web, podemos capturar o erro `error=interaction_required`e enviar de volta o `claims` desafio para a aplicação de ambiente de trabalho. Nesse ponto, a aplicação de ambiente de trabalho pode fazer com que uma nova `acquireToken()` chamar e acrescentar a `claims`desafio como um parâmetro de cadeia de caracteres de consulta adicionais. Essa nova solicitação exige que o utilizador fazer a autenticação multifator e, em seguida, enviar este novo token para o Web API 1 e concluir o fluxo em-nome-de.

Para experimentar este cenário, consulte nosso [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ele demonstra como passar o desafio de afirmações de 1 de API da Web na aplicação nativa e construir um novo pedido dentro da aplicação de cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: Aceder a vários serviços de aplicação

Neste cenário, vamos analisar o caso em que uma aplicação web acessa os dois serviços, um dos quais tem uma política de acesso condicional atribuída. Dependendo de sua lógica de aplicação, pode existir um caminho no qual a aplicação não necessita de acesso a ambos os serviços da web. Neste cenário, a ordem na qual solicitar um token desempenha um papel importante na experiência do utilizador final.

Vamos supor que temos o serviço web A e B e serviço web B tem a nossa política de acesso condicional aplicada. Enquanto o pedido de autenticação interativa inicial necessita de consentimento para ambos os serviços, a política de acesso condicional não é necessário em todos os casos. Se a aplicação solicita um token para o serviço web B, em seguida, a política é invocada e pedidos subsequentes para o serviço web A também é bem-sucedida da seguinte forma.

![Aceder ao diagrama de fluxo de vários serviços de aplicação](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Em alternativa, se a aplicação inicialmente solicitar um token do serviço web, o utilizador final não invoca a política de acesso condicional. Isso permite que o programador da aplicação para controlo de experiência de utilizador final e não forçar a política de acesso condicional para ser invocada em todos os casos. O caso de complicado é se a aplicação, em seguida, pede um token para o serviço web B. Neste momento, o utilizador final tem de estar em conformidade com a política de acesso condicional. Quando a aplicação tenta `acquireToken`, pode gerar o seguinte erro (ilustrado no diagrama seguinte):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aceder a vários serviços pedir um novo token de aplicação](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se a aplicação estiver a utilizar a biblioteca ADAL, uma falha ao adquirir o token é sempre repetida interativamente. Quando ocorre este pedido interativo, o utilizador final tem a oportunidade para estar em conformidade com o acesso condicional. Isso é verdade, a menos que o pedido é uma `AcquireTokenSilentAsync` ou `PromptBehavior.Never` caso em que a aplicação precisa de realizar uma interativo ```AcquireToken``` pedido para dar a utilização de fim a oportunidade para estar em conformidade com a política.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Cenário: Aplicação de página única (SPA) usando ADAL.js

Neste cenário, vamos analisar o caso quando temos uma aplicação de página única (SPA), usando ADAL.js para chamar uma API da web de acesso condicional protegido. Esta é uma arquitetura de simple, mas tem algumas nuances que precisam ser levados em consideração ao desenvolver em torno do acesso condicional.

ADAL.js, existem algumas funções de obter os tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, e `acquireTokenRedirect(…)`.

* `login()` obtém um token de ID através de um pedido de início de sessão interativo, mas não obter tokens de acesso para qualquer serviço (incluindo uma API da web de acesso condicional protegido).
* `acquireToken(…)` em seguida, pode ser utilizado para silenciosamente obter um token de acesso que significa que ele não mostra da interface do Usuário em qualquer circunstância.
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` são usados para interativamente solicitar um token para um recurso que significa que sempre mostram o início de sessão da interface do Usuário.

Quando uma aplicação precisar de um token de acesso para chamar uma API Web, ele tenta uma `acquireToken(…)`. Se a sessão de token expirou ou é necessário estar em conformidade com uma política de acesso condicional, o *acquireToken* falha de função e a aplicação utiliza `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Aplicação de página única com o diagrama de fluxo da ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Vamos examinar um exemplo com o nosso cenário de acesso condicional. O utilizador final apenas foi colocada no site e não tem uma sessão. Realizamos uma `login()` chamar, obter uma ID de token sem o multi-factor authentication. Em seguida, o usuário acessa um botão que requer a aplicação aos dados de pedido de uma API web. A aplicação tenta efetuar um `acquireToken()` chamada falhar, mas uma vez que o utilizador não realizou ainda o multi-factor authentication e tem de estar em conformidade com a política de acesso condicional.

O Azure AD envia a resposta HTTP seguinte:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

A nossa aplicação tem de capturar o `error=interaction_required`. O aplicativo, em seguida, pode utilizar tanto `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O utilizador é forçado a fazer uma autenticação multifator. Depois do utilizador conclui a autenticação multifator, a aplicação é emitida um token de acesso de novo para o recurso pedido.

Para experimentar este cenário, consulte nosso [exemplo de código em-nome-de JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Este exemplo de código utiliza a política de acesso condicional e a web API registada anteriormente com um JS SPA para demonstrar este cenário. Ele mostra como lidar com o desafio de afirmações e obter um token de acesso que pode ser utilizado para a API Web adequadamente. Em alternativa, Check-out gerais [exemplo de código do angular](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) para obter orientações sobre um SPA Angular

## <a name="see-also"></a>Consulte também

* Para saber mais sobre as funcionalidades, veja [acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Para exemplos de código mais do Azure AD, consulte [repositório do GitHub de exemplos de código](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Para obter mais informações sobre do ADAL SDK e o acesso a documentação de referência, consulte [guia de biblioteca](active-directory-authentication-libraries.md).
* Para saber mais sobre cenários de vários inquilinos, consulte [como iniciar sessão de utilizadores que utilizam o padrão de multi-inquilino](howto-convert-app-to-be-multi-tenant.md).
