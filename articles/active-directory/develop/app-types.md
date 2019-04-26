---
title: Tipos de aplicativos na versão 1.0 | Azure
description: Descreve os tipos de aplicações e os cenários suportados pelo ponto de final de v2.0 do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef180fb444e32e8b055837fd418e21162ff58339
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411203"
---
# <a name="application-types-in-v10"></a>Tipos de aplicativos na versão 1.0

Suporta autenticação do Active Directory (Azure AD) do Azure para uma variedade de arquiteturas de aplicações modernas, todos eles com base em protocolos de norma da indústria OAuth 2.0 ou OpenID Connect.

O diagrama seguinte ilustra os cenários e tipos de aplicativos, e como componentes diferentes podem ser adicionados:

![Tipos de Aplicação e cenários](./media/authentication-scenarios/application_types_and_scenarios.png)

Estes são os cinco cenários de aplicação principal suportados pelo Azure AD:

- **[Aplicação de página única (SPA)](single-page-application.md)**: O utilizador precisa de iniciar sessão a uma aplicação de página única que está protegida pelo Azure AD.
- **[Navegador da Web para a aplicação web](web-app.md)**: O utilizador precisa de iniciar sessão a uma aplicação web que está protegida pelo Azure AD.
- **[Aplicação nativa a web API](native-app.md)**: Um aplicativo nativo que é executado num telefone, tablet ou PC tem de autenticar um usuário para obter recursos de uma API web que está protegida pelo Azure AD.
- **[Aplicação Web API Web](web-api.md)**: Uma aplicação web tem de obter recursos de uma API web protegida pelo Azure AD.
- **[O daemon ou servidor de aplicativo web API](service-to-service.md)**: Um aplicativo de daemon ou um aplicativo de servidor sem interface do usuário de web tem de obter recursos de uma API web protegida pelo Azure AD.

Siga as ligações para saber mais sobre cada tipo de aplicação e compreender os cenários de alto nível antes de começar a trabalhar com o código. Também pode saber mais sobre as diferenças que precisa saber ao escrever uma aplicação específica que funciona com o ponto final da versão 1.0 ou o ponto final v2.0.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários do Azure AD e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

Pode desenvolver a qualquer uma das aplicações e dos cenários descritos aqui, usando vários idiomas e plataformas. Todos são apoiados por exemplos de código completo disponíveis no guia de exemplos de código: [v1.0 exemplos de código por cenário](sample-v1-code.md) e [exemplos de código v2.0 por cenário](sample-v2-code.md). Também pode transferir os exemplos de código diretamente a partir do correspondente [repositórios de exemplo do GitHub](https://github.com/Azure-Samples?q=active-directory).

Além disso, se seu aplicativo precisar de uma parte específica ou um segmento de um cenário ponto-a-ponto, na maioria dos casos essa funcionalidade pode ser adicionada independentemente. Por exemplo, se tiver um aplicativo nativo que chama uma API web, pode adicionar facilmente uma aplicação web que também chama a API web.

## <a name="app-registration"></a>Registo de aplicações

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registar uma aplicação que utiliza o ponto de extremidade de versão 1.0 do Azure AD

Qualquer aplicativo que terceiriza a autenticação para o Azure AD tem de estar registado num diretório. Este passo envolve Azure AD a informar sobre a sua aplicação, incluindo o URL onde este se encontra, o URL para enviar respostas após a autenticação, o URI para identificar a sua aplicação e muito mais. Esta informação é necessária para alguns dos motivos principais:

* O Azure AD precisa para comunicar com o aplicativo ao processar o início de sessão ou trocas tokens. As informações passadas entre o Azure AD e o aplicativo inclui o seguinte:
  
  * **URI de ID de aplicação** -o identificador para uma aplicação. Este valor é enviado para o Azure AD durante a autenticação para indicar qual aplicativo o chamador deseja um token para. Além disso, este valor está incluído no token, para que a aplicação saiba que era o destino pretendido.
  * **URL de resposta** e **URI de redirecionamento** -para uma web API ou aplicação web, o URL de resposta é a localização onde o Azure AD irá enviar a resposta de autenticação, incluindo um token, se a autenticação foi efetuada com êxito. Para um aplicativo nativo, o URI de redirecionamento é um identificador exclusivo para o qual o Azure AD irá redirecionar o agente do usuário numa solicitação de OAuth 2.0.
  * **ID da aplicação** -o ID para uma aplicação, o que é gerada pelo Azure AD quando a aplicação fica registada. Quando pedir um código de autorização ou token, o ID da aplicação e a chave são enviadas para o Azure AD durante a autenticação.
  * **Chave** -a chave que é enviada com um ID de aplicação durante a autenticação para o Azure AD para chamar uma API web.
* O Azure AD precisa garantir que a aplicação tem as permissões necessárias para aceder aos seus dados de diretório, outras aplicações na sua organização e assim por diante.

Para obter detalhes, saiba como [registar uma aplicação com o ponto de final do Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplicações de inquilino único e multi-inquilinos

Aprovisionamento ficará mais claro quando compreender o que há duas categorias de aplicações que podem ser desenvolvidas e integradas com o Azure AD:

* **Única aplicação de inquilino** -uma aplicação de inquilino único destina-se a utilização de uma organização. São, normalmente, linha de negócio (LoB) aplicativos escritos por um programador empresarial. Uma aplicação de inquilino individual apenas tem de ser acedidos por utilizadores num diretório e, assim, ele só precisa ser aprovisionado num diretório. Esses aplicativos são, normalmente, registados por um desenvolvedor na organização.
* **Aplicação multi-inquilino** -uma aplicação multi-inquilino destina-se para uso em muitas organizações, não apenas uma organização. Estas são normalmente software-como-serviço (SaaS) aplicações escritas por um fabricante de software independente (ISV). Aplicações de multi-inquilinos tem de ser aprovisionado em cada diretório em que serão utilizadas, necessita de consentimento do utilizador ou administrador para registá-los. Este processo de consentimento é iniciado quando uma aplicação foi registrada no diretório e é dado acesso para a Graph API ou talvez outro web API. Quando um utilizador ou administrador a partir de uma organização diferente se inscreve para utilizar a aplicação, é-lhes apresentada uma caixa de diálogo que apresenta as permissões que o aplicativo requer. O utilizador ou administrador pode, em seguida, autorizar o aplicativo, que dá à aplicação acesso aos dados declarados e, finalmente, regista o aplicativo em seu diretório. Para obter mais informações, consulte [descrição geral da estrutura de consentimento](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Considerações adicionais durante o desenvolvimento de aplicações de inquilino ou multi-inquilino de único

Algumas considerações adicionais surgirem ao desenvolver uma aplicação multi-inquilino, em vez de uma aplicação de inquilino único. Por exemplo, se estiver fazendo seu aplicativo disponível para utilizadores em vários diretórios, terá um mecanismo para determinar qual o inquilino estão na. Uma aplicação de inquilino único apenas precisa de ter um aspeto em seu próprio diretório para um utilizador, enquanto uma aplicação de multi-inquilino precisa de identificar um utilizador específico no todos os diretórios no Azure AD. Para realizar esta tarefa, o Azure AD fornece um ponto de extremidade autenticação comum em que qualquer aplicação multi-inquilino pode direcionar o início de sessão solicitações, em vez de um ponto de extremidade específico de inquilino. Este ponto final fizer https://login.microsoftonline.com/common para todos os diretórios no Azure AD, enquanto que um ponto de extremidade específico de inquilino pode ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto de extremidade comum é especialmente importante a ter em consideração quando desenvolver a sua aplicação, porque terá da lógica necessária para lidar com vários inquilinos durante o início de sessão, fim de sessão e a validação do token.

Se estiver atualmente a desenvolver uma aplicação de inquilino único, mas pretende disponibilizar para muitas organizações, pode facilmente efetuar alterações para a aplicação e a respetiva configuração no Azure AD para torná-lo a multi-inquilino com capacidade. Além disso, o Azure AD utiliza a mesma chave de assinatura para todos os tokens em todos os diretórios, se está a fornecer autenticação num único inquilino ou aplicação multi-inquilino.

Cada cenário listado neste documento inclui uma subseção que descreve os requisitos de aprovisionamento. Para mais informações sobre aprovisionamento de uma aplicação no Azure AD e as diferenças entre aplicativos de únicas e multi-inquilinos, consulte [integrar aplicações com o Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) para obter mais informações. Continue a ler para compreender os cenários comuns de aplicação no Azure AD.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre outro do Azure AD [Noções básicas de autenticação](authentication-scenarios.md)
