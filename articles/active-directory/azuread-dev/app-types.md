---
title: Tipos de aplicação em v1.0 | Rio Azure
description: Descreve os tipos de apps e cenários suportados pelo Azure Ative Directory v2.0 endpoint.
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
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88116449"
---
# <a name="application-types-in-v10"></a>Tipos de aplicação em v1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Azure Ative Directory (Azure AD) suporta a autenticação para uma variedade de arquiteturas modernas de aplicações, todas elas baseadas em protocolos padrão da indústria OAuth 2.0 ou OpenID Connect.

O diagrama a seguir ilustra os cenários e tipos de aplicação e como podem ser adicionados diferentes componentes:

![Tipos de Aplicação e cenários](./media/authentication-scenarios/application-types-scenarios.png)

Estes são os cinco cenários de candidatura primária apoiados pela Azure AD:

- **[Aplicação de uma página única (SPA)](single-page-application.md)**: Um utilizador precisa de iniciar sação numa aplicação de uma página que seja protegida pelo Azure AD.
- **[Web browser para aplicação web](web-app.md)**: Um utilizador precisa de iniciar sação numa aplicação web que é protegida pelo Azure AD.
- **[Aplicação nativa para web API](native-app.md)**: Uma aplicação nativa que funciona em um telefone, tablet ou PC precisa autenticar um utilizador para obter recursos de uma API web que é protegida pela Azure AD.
- **[Aplicação web para web API](web-api.md)**: Uma aplicação web precisa de obter recursos de uma API web protegida pela Azure AD.
- **[Aplicação da Daemon ou servidor para web API](service-to-service.md)**: Uma aplicação daemon ou uma aplicação de servidor sem interface de utilizador web precisa de obter recursos de uma API web protegida pela Azure AD.

Siga os links para saber mais sobre cada tipo de aplicação e compreenda os cenários de alto nível antes de começar a trabalhar com o código. Também pode aprender sobre as diferenças que precisa de saber ao escrever uma determinada aplicação que funciona com o ponto final v1.0 ou v2.0.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários e funcionalidades do AD Azure. Para determinar se deve utilizar o ponto final v2.0, leia sobre [as limitações v2.0](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json).

Pode desenvolver qualquer uma das aplicações e cenários descritos aqui usando vários idiomas e plataformas. Todas elas são apoiadas por amostras de código completas disponíveis no guia de amostras de código: [amostras de código v1.0 por cenário](sample-v1-code.md) e [amostras de código v2.0 por cenário](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Também pode descarregar as amostras de código diretamente dos [repositórios de amostras do GitHub correspondentes.](https://github.com/Azure-Samples?q=active-directory)

Além disso, se a sua aplicação necessitar de uma peça ou segmento específico de um cenário de ponta a ponta, na maioria dos casos essa funcionalidade pode ser adicionada de forma independente. Por exemplo, se tiver uma aplicação nativa que chama uma API web, pode facilmente adicionar uma aplicação web que também chama a API web.

## <a name="app-registration"></a>Registo de aplicações

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registar uma aplicação que utiliza o ponto final Azure AD v1.0

Qualquer aplicação que subcontrata a autenticação para a Azure AD deve estar registada num diretório. Este passo envolve dizer ao Azure AD sobre a sua aplicação, incluindo o URL onde está localizado, o URL para enviar respostas após a autenticação, o URI para identificar a sua aplicação, e muito mais. Estas informações são necessárias por algumas razões fundamentais:

* O Azure AD precisa de comunicar com a aplicação ao manusear fichas de inscrição ou troca de fichas. As informações transmitidas entre a Azure AD e a aplicação incluem o seguinte:
  
  * **ID URI** de aplicação - O identificador para uma aplicação. Este valor é enviado para a Azure AD durante a autenticação para indicar qual a aplicação para a qual o chamador quer um símbolo. Além disso, este valor está incluído no token de modo que a aplicação sabe que era o alvo pretendido.
  * **URL de resposta** e **redirecionamento URI** - Para uma aplicação web API ou web, o URL de resposta é o local onde a Azure AD enviará a resposta de autenticação, incluindo um token se a autenticação tiver sido bem sucedida. Para uma aplicação nativa, o Redirect URI é um identificador único ao qual a Azure AD redirecionará o utilizador-agente num pedido de OAuth 2.0.
  * **ID de aplicação** - O ID para uma aplicação, que é gerado pela Azure AD quando a aplicação está registada. Ao solicitar um código de autorização ou ficha, o ID e a Chave de Aplicação são enviados para a Azure AD durante a autenticação.
  * **Chave** - A chave que é enviada juntamente com um ID de aplicação ao autenticar a AZure AD para chamar uma API web.
* A Azure AD precisa de garantir que a aplicação tem as permissões necessárias para aceder aos seus dados de diretório, outras aplicações na sua organização, e assim por diante.

Para mais detalhes, saiba como [registar uma aplicação.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplicativos de inquilino único e multi-inquilinos

O provisionamento torna-se mais claro quando se compreende que existem duas categorias de aplicações que podem ser desenvolvidas e integradas com a Azure AD:

* **Pedido de inquilino único** - Um pedido de inquilino único destina-se a ser usado numa organização. Estas são aplicações tipicamente de linha de negócio (LoB) escritas por um desenvolvedor de empresas. Uma única aplicação de inquilino só precisa de ser acedida pelos utilizadores num só diretório e, consequentemente, só precisa de ser a provisionada num diretório. Estas aplicações são normalmente registadas por um desenvolvedor na organização.
* **Aplicação multi-arrendatário** - Uma aplicação multi-inquilino destina-se a ser usada em muitas organizações, e não apenas numa organização. Estas são aplicações tipicamente software-as-a-service (SaaS) escritas por um fornecedor de software independente (ISV). As aplicações multi-arrendatários devem ser a provisionadas em cada diretório onde serão utilizadas, o que requer o consentimento do utilizador ou do administrador para as registar. Este processo de consentimento começa quando um pedido foi registado no diretório e é dado acesso à API do Gráfico ou talvez a outra API web. Quando um utilizador ou administrador de uma organização diferente se inscreve para utilizar a aplicação, é-lhes apresentado um diálogo que exibe as permissões que a aplicação requer. O utilizador ou administrador pode então consentir com a aplicação, que dá acesso à aplicação aos dados indicados, e finalmente regista a aplicação no seu diretório. Para mais informações, consulte [a visão geral do Quadro de Consentimento.](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Considerações adicionais ao desenvolver aplicações individuais de inquilinos ou multi-inquilinos

Algumas considerações adicionais surgem quando se desenvolve uma aplicação multi-arrendatário em vez de um único pedido de inquilino. Por exemplo, se estiver a disponibilizar a sua aplicação aos utilizadores em vários diretórios, precisa de um mecanismo para determinar em que inquilino estão. Uma única aplicação de inquilino só precisa de procurar no seu próprio diretório para um utilizador, enquanto uma aplicação multi-arrendatário precisa identificar um utilizador específico de todos os diretórios em Azure AD. Para realizar esta tarefa, a Azure AD fornece um ponto final de autenticação comum onde qualquer aplicação multi-inquilino pode dirigir pedidos de inscrição, em vez de um ponto final específico do inquilino. Este ponto final é `https://login.microsoftonline.com/common` para todos os diretórios em Azure AD, enquanto um ponto final específico do inquilino pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com` . O ponto final comum é especialmente importante a considerar ao desenvolver a sua aplicação porque você precisará da lógica necessária para lidar com vários inquilinos durante a sposição, súmis e validação simbólica.

Se está atualmente a desenvolver uma única aplicação de inquilino, mas quer disponibilizá-la a muitas organizações, pode facilmente fazer alterações na aplicação e na sua configuração em Azure AD para torná-la multi-arrendatária capaz. Além disso, a Azure AD usa a mesma chave de assinatura para todos os tokens em todos os diretórios, quer esteja a fornecer autenticação num único inquilino ou aplicação multi-arrendatário.

Cada cenário listado neste documento inclui uma subsecção que descreve os seus requisitos de provisionamento. Para obter informações mais aprofundadas sobre o fornecimento de um pedido no Azure AD e as diferenças entre aplicações individuais e multi-arrendatários, consulte [a Integração de aplicações com o Azure Ative Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para obter mais informações. Continuar a ler Para compreender os cenários comuns de candidatura em Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [fundamentos básicos de autenticação](v1-authentication-scenarios.md) AD Azure