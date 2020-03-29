---
title: Tipos de candidatura em v1.0 / Azure
description: Descreve os tipos de apps e cenários suportados pelo Ponto final do Azure Ative Directory v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c290cbf36fd53d5afb5fd805cda896fb6879bb4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154955"
---
# <a name="application-types-in-v10"></a>Tipos de aplicação em v1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Azure Ative Directory (Azure AD) suporta a autenticação para uma variedade de arquiteturas de aplicações modernas, todas elas baseadas em protocolos padrão da indústria OAuth 2.0 ou OpenID Connect.

O diagrama que se segue ilustra os cenários e os tipos de aplicação e como podem ser adicionados diferentes componentes:

![Tipos de Aplicação e cenários](./media/authentication-scenarios/application-types-scenarios.png)

Estes são os cinco cenários de candidatura primária suportados pela Azure AD:

- **[Aplicação de página única (SPA)](single-page-application.md)**: Um utilizador precisa de iniciar sessão numa aplicação de uma única página que é protegida pela Azure AD.
- **[Navegador web para aplicação web](web-app.md)**: Um utilizador precisa de iniciar sessão numa aplicação web que é protegida pela Azure AD.
- **[Aplicação nativa para web API](native-app.md)**: Uma aplicação nativa que funciona num telefone, tablet ou PC precisa de autenticar um utilizador para obter recursos de uma API web que é protegida pela Azure AD.
- **[Aplicação web para Web API](web-api.md)**: Uma aplicação web precisa de obter recursos de uma API web protegida pela Azure AD.
- **[Aplicação da ememon ou servidor para web API](service-to-service.md)**: Uma aplicação daemon ou uma aplicação de servidor sem interface de utilizador web precisa de obter recursos de uma API web protegida por Azure AD.

Siga os links para saber mais sobre cada tipo de app e compreenda os cenários de alto nível antes de começar a trabalhar com o código. Também pode aprender sobre as diferenças que precisa de saber ao escrever uma determinada aplicação que funciona com o ponto final v1.0 ou v2.0 endpoint.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários e funcionalidades da Azure AD. Para determinar se deve utilizar o ponto final v2.0, leia sobre [as limitações v2.0](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Pode desenvolver qualquer uma das aplicações e cenários aqui descritos utilizando vários idiomas e plataformas. Todas elas são apoiadas por amostras completas de código disponíveis no guia de amostras de código: [v1.0 amostras de código por cenário](sample-v1-code.md) e amostras de código [v2.0 por cenário](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Também pode descarregar as amostras de código diretamente dos [repositórios](https://github.com/Azure-Samples?q=active-directory)correspondentes da amostra GitHub.

Além disso, se a sua aplicação precisar de uma peça ou segmento específico de um cenário de ponta a ponta, na maioria dos casos essa funcionalidade pode ser adicionada de forma independente. Por exemplo, se tiver uma aplicação nativa que chama a API web, pode facilmente adicionar uma aplicação web que também chama a Web API.

## <a name="app-registration"></a>Registo da aplicação

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registar uma aplicação que utiliza o ponto final da AD Azure

Qualquer aplicação que forme a autenticação para a AD Azure deve ser registada num diretório. Este passo envolve contar ao Azure AD sobre a sua aplicação, incluindo o URL onde está localizado, o URL para enviar respostas após a autenticação, o URI para identificar a sua aplicação, e muito mais. Esta informação é necessária por algumas razões fundamentais:

* A Azure AD precisa de comunicar com a aplicação ao manusear sinais ou trocar fichas. As informações transmitidas entre a Azure AD e a aplicação incluem:
  
  * **ID DE aplicação URI** - O identificador para uma aplicação. Este valor é enviado para a AD Azure durante a autenticação para indicar para que aplicação o chamador quer um símbolo. Adicionalmente, este valor está incluído no símbolo para que a aplicação saiba que foi o alvo pretendido.
  * **Url de resposta** e **Redirecionamento URI** - Para uma Web API ou aplicação web, o URL de resposta é o local onde o Azure AD enviará a resposta de autenticação, incluindo um símbolo se a autenticação tiver sido bem sucedida. Para uma aplicação nativa, o Redirect URI é um identificador único para o qual a Azure AD redirecionará o agente utilizador num pedido oAuth 2.0.
  * **ID de aplicação** - O ID para uma aplicação, que é gerado pela Azure AD quando a aplicação é registada. Ao solicitar um código de autorização ou ficha, o ID de aplicação e a chave são enviados para a AD Azure durante a autenticação.
  * **Chave** - A chave que é enviada juntamente com um ID de aplicação ao autenticar a AD Azure para chamar uma API web.
* A Azure AD precisa de garantir que a aplicação tem as permissões necessárias para aceder aos dados do seu diretório, outras aplicações na sua organização, e assim por diante.

Para mais detalhes, saiba [como registar uma aplicação](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplicativos de inquilino único e multi-inquilinos

O provisionamento torna-se mais claro quando se compreende que existem duas categorias de aplicações que podem ser desenvolvidas e integradas com a Azure AD:

* **Pedido** de inquilino único - Um único pedido de inquilino destina-se a ser utilizado numa só organização. Estas são aplicações tipicamente line-of-business (LoB) escritas por um desenvolvedor de empresas. Uma única aplicação de inquilino só precisa de ser acedida pelos utilizadores num só diretório e, consequentemente, só precisa de ser disponibilizada num só diretório. Estas aplicações são tipicamente registadas por um desenvolvedor da organização.
* **Aplicação multi-inquilino** - Uma aplicação multi-inquilino destina-se a ser usada em muitas organizações, e não apenas numa organização. Estas são aplicações tipicamente de software como um serviço (SaaS) escritas por um fornecedor de software independente (ISV). Os pedidos de multi-inquilinos devem ser aprovisionados em cada diretório onde serão utilizados, o que requer o consentimento do utilizador ou administrador para os registar. Este processo de consentimento começa quando uma aplicação foi registada no diretório e tem acesso à API do Gráfico ou talvez a outra API web. Quando um utilizador ou administrador de uma organização diferente se inscreve para usar a aplicação, é-lhes apresentado um diálogo que apresenta as permissões que a aplicação necessita. O utilizador ou administrador pode então consentir com a aplicação, que dá à aplicação acesso aos dados indicados, e finalmente regista a aplicação no seu diretório. Para mais informações, consulte [a visão geral do Quadro de Consentimento](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Considerações adicionais ao desenvolver aplicações individuais de inquilinoou multi-inquilinos

Algumas considerações adicionais surgem no desenvolvimento de um pedido de multi-inquilinoem de um único pedido de inquilino. Por exemplo, se estiver a disponibilizar a sua aplicação aos utilizadores em vários diretórios, precisa de um mecanismo para determinar em que inquilino estão. Uma única aplicação de inquilino só precisa de olhar para um utilizador no seu próprio diretório, enquanto uma aplicação multi-arrendatária precisa identificar um utilizador específico de todos os diretórios da AD Azure. Para realizar esta tarefa, a Azure AD fornece um ponto final de autenticação comum onde qualquer aplicação multi-arrendatária pode dirigir pedidos de inscrição, em vez de um ponto final específico do inquilino. Este ponto `https://login.microsoftonline.com/common` final é para todos os diretórios em Azure AD, enquanto um ponto final específico do inquilino pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com`. O ponto final comum é especialmente importante a considerar ao desenvolver a sua aplicação porque você precisará da lógica necessária para lidar com vários inquilinos durante a inscrição, inscrição e validação de fichas.

Se está atualmente a desenvolver uma única aplicação de inquilino, mas quer disponibilizá-la a muitas organizações, pode facilmente fazer alterações na aplicação e na sua configuração em Azure AD para torná-la multi-arrendatária capaz. Além disso, a Azure AD utiliza a mesma chave de assinatura para todos os tokens em todos os diretórios, quer esteja a fornecer autenticação num único pedido de inquilino ou multi-inquilino.

Cada cenário listado neste documento inclui uma subsecção que descreve os seus requisitos de provisionamento. Para obter informações mais aprofundadas sobre o fornecimento de uma aplicação em Azure AD e as diferenças entre aplicações individuais e multi-arrendatárias, consulte a Integração de aplicações com o [Diretório Ativo Azure](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para obter mais informações. Continuar a ler Para compreender os cenários comuns de candidatura em Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [fundamentos de autenticação](v1-authentication-scenarios.md) da AD Azure
