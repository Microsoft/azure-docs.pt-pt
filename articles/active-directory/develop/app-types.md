---
title: Tipos de aplicativos em v 1.0 | Azure
description: Descreve os tipos de aplicativos e cenários com suporte pelo ponto de extremidade Azure Active Directory v 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e4228febb5b49ad14a89ceec5482c277fca5fae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844214"
---
# <a name="application-types-in-v10"></a>Tipos de aplicativos em v 1.0

O Azure Active Directory (AD do Azure) dá suporte à autenticação para uma variedade de arquiteturas de aplicativo modernas, todas elas baseadas em protocolos padrão do setor OAuth 2,0 ou OpenID Connect.

O diagrama a seguir ilustra os cenários e os tipos de aplicativos e como os diferentes componentes podem ser adicionados:

![Tipos de Aplicação e cenários](./media/authentication-scenarios/application_types_and_scenarios.png)

Estes são os cinco cenários principais de aplicativo com suporte do Azure AD:

- **[Aplicativo de página única (Spa)](single-page-application.md)** : um usuário precisa entrar em um aplicativo de página única que é protegido pelo Azure AD.
- **[Navegador da Web para aplicativo Web](web-app.md)** : um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD.
- **[Aplicativo nativo para API da Web](native-app.md)** : um aplicativo nativo que é executado em um telefone, Tablet ou PC precisa autenticar um usuário para obter recursos de uma API da Web que é protegida pelo Azure AD.
- **[Aplicativo Web para API da Web](web-api.md)** : um aplicativo Web precisa obter recursos de uma API da Web protegida pelo Azure AD.
- **[Aplicativo de daemon ou de servidor para API da Web](service-to-service.md)** : um aplicativo de daemon ou um aplicativo de servidor sem interface de usuário da Web precisa obter recursos de uma API da Web protegida pelo Azure AD.

Siga os links para saber mais sobre cada tipo de aplicativo e entender os cenários de alto nível antes de começar a trabalhar com o código. Você também pode aprender sobre as diferenças que precisa saber ao escrever um aplicativo específico que funciona com o ponto de extremidade v 1.0 ou o ponto de extremidade v 2.0.

> [!NOTE]
> O ponto de extremidade v 2.0 não dá suporte a todos os cenários e recursos do Azure AD. Para determinar se você deve usar o ponto de extremidade v 2.0, leia sobre as [limitações de v 2.0](active-directory-v2-limitations.md).

Você pode desenvolver qualquer um dos aplicativos e cenários descritos aqui usando várias linguagens e plataformas. Eles são todos apoiados por exemplos de código completos disponíveis no guia de exemplos de código: os exemplos de código do [v 1.0 por cenário](sample-v1-code.md) e [exemplos de código v 2.0 por cenário](sample-v2-code.md). Você também pode baixar os exemplos de código diretamente dos [repositórios de exemplo do GitHub](https://github.com/Azure-Samples?q=active-directory)correspondentes.

Além disso, se seu aplicativo precisar de uma parte ou segmento específico de um cenário de ponta a ponta, na maioria dos casos essa funcionalidade pode ser adicionada de forma independente. Por exemplo, se você tiver um aplicativo nativo que chama uma API da Web, poderá adicionar facilmente um aplicativo Web que também chama a API da Web.

## <a name="app-registration"></a>Registo da aplicação

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrando um aplicativo que usa o ponto de extremidade v 1.0 do Azure AD

Qualquer aplicativo que terceiriza a autenticação para o Azure AD deve ser registrado em um diretório. Esta etapa envolve informar ao Azure AD sobre seu aplicativo, incluindo a URL onde ele está localizado, a URL para enviar respostas após a autenticação, o URI para identificar seu aplicativo e muito mais. Essas informações são necessárias por alguns motivos principais:

* O Azure AD precisa se comunicar com o aplicativo ao manipular tokens de logon ou de troca. As informações passadas entre o Azure AD e o aplicativo incluem o seguinte:
  
  * **URI da ID do aplicativo** -o identificador de um aplicativo. Esse valor é enviado ao Azure AD durante a autenticação para indicar a qual aplicativo o chamador deseja um token. Além disso, esse valor é incluído no token para que o aplicativo saiba que ele era o destino pretendido.
  * **URL de resposta** e **URI de redirecionamento** – para uma API Web ou aplicativo Web, a URL de resposta é o local em que o Azure ad enviará a resposta de autenticação, incluindo um token se a autenticação tiver sido bem-sucedida. Para um aplicativo nativo, o URI de redirecionamento é um identificador exclusivo para o qual o Azure AD redirecionará o agente do usuário em uma solicitação do OAuth 2,0.
  * **ID do aplicativo** – a ID de um aplicativo, que é gerada pelo Azure ad quando o aplicativo é registrado. Ao solicitar um código de autorização ou token, a ID e a chave do aplicativo são enviadas ao Azure AD durante a autenticação.
  * **Chave** -a chave que é enviada junto com uma ID de aplicativo ao autenticar no Azure ad para chamar uma API da Web.
* O Azure AD precisa garantir que o aplicativo tenha as permissões necessárias para acessar os dados do diretório, outros aplicativos em sua organização e assim por diante.

Para obter detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplicativos de locatário único e de vários locatários

O provisionamento fica mais claro quando você entende que há duas categorias de aplicativos que podem ser desenvolvidos e integrados ao Azure AD:

* **Aplicativo de locatário único** -um aplicativo de locatário único destina-se ao uso em uma organização. Esses são normalmente aplicativos de linha de negócios (LoB) escritos por um desenvolvedor empresarial. Um único aplicativo de locatário só precisa ser acessado por usuários em um diretório e, como resultado, ele só precisa ser provisionado em um diretório. Esses aplicativos normalmente são registrados por um desenvolvedor na organização.
* **Aplicativo multilocatário** -um aplicativo multilocatário destina-se ao uso em muitas organizações, não apenas em uma organização. Esses são normalmente aplicativos de software como serviço (SaaS) escritos por um fornecedor independente de software (ISV). Aplicativos multilocatários precisam ser provisionados em cada diretório onde serão usados, o que exige o consentimento do usuário ou administrador para registrá-los. Esse processo de consentimento é iniciado quando um aplicativo é registrado no diretório e recebe acesso ao API do Graph ou talvez a outra API da Web. Quando um usuário ou administrador de uma organização diferente se inscreve para usar o aplicativo, ele recebe uma caixa de diálogo que exibe as permissões exigidas pelo aplicativo. O usuário ou administrador pode então consentir o aplicativo, que dá ao aplicativo acesso aos dados declarados e, por fim, registra o aplicativo em seu diretório. Para obter mais informações, consulte [visão geral da estrutura de consentimento](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Considerações adicionais ao desenvolver aplicativos de locatário único ou de vários locatários

Algumas considerações adicionais surgem ao desenvolver um aplicativo multilocatário em vez de um aplicativo de locatário único. Por exemplo, se você estiver disponibilizando seu aplicativo para os usuários em vários diretórios, você precisará de um mecanismo para determinar em qual locatário eles estão. Um aplicativo de locatário único precisa apenas examinar seu próprio diretório para um usuário, enquanto um aplicativo multilocatário precisa identificar um usuário específico de todos os diretórios no Azure AD. Para realizar essa tarefa, o AD do Azure fornece um ponto de extremidade de autenticação comum em que qualquer aplicativo multilocatário pode direcionar solicitações de entrada, em vez de um ponto de extremidade específico do locatário. Esse ponto de extremidade é https://login.microsoftonline.com/common para todos os diretórios no Azure AD, enquanto um ponto de extremidade específico de locatário pode ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto de extremidade comum é especialmente importante para ser considerado ao desenvolver seu aplicativo, pois você precisará da lógica necessária para lidar com vários locatários durante a entrada, saída e validação de token.

Se, no momento, você estiver desenvolvendo um aplicativo de locatário único, mas quiser disponibilizá-lo para muitas organizações, poderá fazer alterações facilmente no aplicativo e em sua configuração no Azure AD para torná-lo compatível com vários locatários. Além disso, o Azure AD usa a mesma chave de assinatura para todos os tokens em todos os diretórios, independentemente de você estar fornecendo autenticação em um único locatário ou aplicativo multilocatário.

Cada cenário listado neste documento inclui uma subseção que descreve seus requisitos de provisionamento. Para obter informações mais detalhadas sobre como provisionar um aplicativo no Azure AD e as diferenças entre aplicativos únicos e multilocatários, consulte [integrando aplicativos com Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) para obter mais informações. Continue lendo para entender os cenários comuns de aplicativos no Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outros [conceitos básicos de autenticação](v1-authentication-scenarios.md) do Azure AD
