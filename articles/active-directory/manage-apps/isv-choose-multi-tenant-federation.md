---
title: Selecione o protocolo de Federação certo para a sua aplicação multi-inquilino
description: Documentação de orientação para fornecedores independentes de software sobre como integrar com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795204"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Selecione o protocolo de Federação certo para a sua aplicação multi-inquilino

Quando desenvolve o seu software como um aplicativo de serviço (SaaS), tem de selecionar o protocolo de federação que melhor atenda às necessidades de seus e sua dos clientes. Esta decisão baseia-se a sua plataforma de desenvolvimento e o desejo de integrar dados disponíveis no ecossistema dos seus clientes do Office 365 e o Azure AD.

Ver a lista completa dos [protocolos disponíveis para as integrações de SSO](what-is-single-sign-on.md) com o Azure Active Directory.
A seguir compara de tabela 
* Autenticação aberta 2.0 (OAuth 2.0)
* Abrir ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Funcionalidade| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Baseado na Web início de sessão único| √| √ |
| Baseada na Web e o fim de sessão único| √| √ |
| Baseado no Mobile início de sessão único| √| √* |
| Baseado no Mobile o fim de sessão único| √| √* |
| Políticas de acesso condicional para aplicações móveis| √| X |
| Experiência totalmente integrada de MFA para aplicações móveis| √| X |
| Acesso Microsoft Graph| √| X |

\* Possível, mas a Microsoft não fornece exemplos ou orientações.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e abrir ID Connect

OAuth 2.0 é uma [norma da indústria](https://oauth.net/2/) protocolo para autorização. OIDC (OpenID Connect) é uma [norma da indústria](https://openid.net/connect/) camada de autenticação de identidade criada sobre o protocolo Oath 2.0.

### <a name="benefits"></a>Benefícios

A Microsoft recomenda utilizar OIDC/OAuth 2.0, pois têm autenticação e autorização incorporadas para os protocolos. Com o SAML, deve implementar adicionalmente autorização.

A autorização inerente a esses protocolos permite que a sua aplicação aceder e integrar com o usuário avançada e aos dados organizacionais por meio da API do Microsoft Graph.

Utilizar o OAuth 2.0 e OIDC simplifica a experiência do utilizador final dos seus clientes ao adotar o SSO para a sua aplicação. Pode definir facilmente necessário de conjuntos de permissões, que, em seguida, é representado automaticamente para o administrador ou utilizador final que consinta.

Além disso, utilizar esses protocolos permite aos seus clientes utilizar o acesso condicional e políticas MFA para controlar o acesso aos aplicativos. A Microsoft fornece as bibliotecas e [exemplos de código em várias plataformas de tecnologia](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para auxiliar o desenvolvimento.  

### <a name="implementation"></a>Implementação

Registar a sua aplicação com o Microsoft Identity, que é um fornecedor de OAuth 2.0. Também, em seguida, foi possível registar o seu aplicativo com base em OAuth 2.0 com qualquer outro fornecedor de identidade que pretende integrar. 

Para obter informações sobre como registar a sua aplicação e implementar estes protocolos para SSO para aplicações web, consulte [autorizar o acesso a aplicações web com OpenID Connect e Azure Active Directory](../develop/sample-v2-code.md).  Para obter informações sobre como implementar estes protocolos para SSO em aplicações móveis, consulte o seguinte: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plataforma Universal do Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

Security Assertion Markup Language (SAML), normalmente, é utilizado para aplicações web. Ver [como o Azure utiliza o protocolo SAML](../develop/active-directory-saml-protocol-reference.md) para uma descrição geral. 

Web Services Federation (WSFed) é uma [norma da indústria](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente utilizado para aplicações web que são desenvolvidas com a plataforma .net.

### <a name="benefits"></a>Benefícios

SAML 2.0 é um padrão maduro e a maioria dos tecnologia plataformas suporte bibliotecas open-source para SAML 2.0. Pode fornecer uma interface de administração para configurar SAML SSO aos seus clientes. Pode configurar o SAML SSO para o Microsoft Azure AD e qualquer outro fornecedor de identidade que suporte SAML 2

### <a name="trade-offs"></a>Vantagens e desvantagens

Ao utilizar protocolos de SAML 2.0 ou WSFed para aplicativos móveis, determinadas políticas de acesso condicional, incluindo multi-factor Authentication (MFA) tenham uma experiência de degradado. Além disso, se desejar acessar o Microsoft Graph, terá de implementar a autorização através de OAuth 2.0 para gerar tokens necessários. 

### <a name="implementation"></a>Implementação

A Microsoft não fornecer bibliotecas para a implementação de SAML ou Recomendamos bibliotecas específicas. Muitas bibliotecas de código-fonte aberto estão disponíveis.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e através de Rest do Microsoft Graph API 

Microsoft Graph é os recursos de infraestrutura de dados em todos os Microsoft 365, incluindo o Office 365, Windows 10 e Enterprise Mobility e segurança e outros produtos, como o Dynamics 365. Isto inclui os principais esquemas de entidades, como os utilizadores, grupos, calendário, correio, ficheiros e muito mais, produtividade do usuário nessa unidade. Microsoft Graph oferece três interfaces para os desenvolvedores um REST com base em API, ligar a dados do Microsoft Graph e conectores que permitem que os desenvolvedores para adicionar seus próprios dados para o Microsoft Graph.  

Usando qualquer um dos protocolos acima para SSO permite o acesso de seu aplicativo para os dados avançados disponíveis através da API de REST do Microsoft Graph. Isto permite que os clientes podem obter mais valor dos seus investimentos em Microsoft 365. Por exemplo, seu aplicativo pode chamar a API do Microsoft Graph para integrar com a instância dos seus clientes do Office 365 e Microsoft Office dos utilizadores superfície e SharePoint itens na sua aplicação. 

Se estiver a utilizar abrir ID Connect para autenticar, em seguida, a sua experiência de desenvolvimento é totalmente integrada como usará o OAuth2, a base de abrir ID Connect, para adquirir tokens pode ser utilizada para invocar o Microsoft Graph APIs. Se a sua aplicação utilizar SAML ou WSFed, tem de adicionar o código adicional na sua aplicação para obter estes OAuth2 para adquirir os tokens necessários para invocar o Microsoft Graph APIs. 

## <a name="next-steps"></a>Próximos Passos

[Ativar o SSO para a sua aplicação multi-inquilino](isv-sso-content.md)

[Criar documentação para a sua aplicação multi-inquilino](isv-create-sso-documentation.md)
