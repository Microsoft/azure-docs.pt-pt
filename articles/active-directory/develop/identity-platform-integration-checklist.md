---
title: Práticas recomendadas para a plataforma Microsoft Identity | Azure
description: Saiba mais sobre as práticas recomendadas, recomendações e supervisões comuns ao integrar com a plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f4afe1c31ae964aab82664de12144185069af5a
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71145664"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Práticas recomendadas e recomendações da plataforma Microsoft Identity

Este artigo destaca as práticas recomendadas, recomendações e supervisões comuns ao integrar com a plataforma de identidade da Microsoft.  Esta lista de verificação orientará você para uma integração segura e de alta qualidade. Revise essa lista regularmente para garantir que você mantenha a qualidade e a segurança da integração do seu aplicativo com a plataforma de identidade. A lista de verificação não se destina a examinar todo o aplicativo. O conteúdo da lista de verificação está sujeito a alterações à medida que fizermos melhorias na plataforma.

Se você estiver apenas começando, confira a [documentação da plataforma de identidade da Microsoft](index.yml) para saber mais sobre noções básicas de autenticação, cenários de aplicativos na plataforma de identidade da Microsoft e muito mais.

Use a seguinte lista de verificação para garantir que seu aplicativo seja efetivamente integrado à [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Noções Básicas

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Leia e entenda as [políticas da plataforma Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Verifique se seu aplicativo segue os termos descritos como foram projetados para proteger os usuários e a plataforma. |

## <a name="ownership"></a>Propriedade

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Verifique se as informações associadas à conta que você usou para registrar e gerenciar aplicativos estão atualizadas. |

## <a name="branding"></a>Personalização

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Siga as [diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md). |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça um nome e um logotipo significativos para seu aplicativo. Essas informações são exibidas no [prompt de consentimento do seu aplicativo](application-consent-experience.md). Certifique-se de que seu nome e logotipo sejam representativos da sua empresa/produto para que os usuários possam tomar decisões informadas. Certifique-se de que você não está violando marcas comerciais. |

## <a name="privacy"></a>Privacidade

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça links para os termos de serviço e a política de privacidade do seu aplicativo. |

## <a name="security"></a>Segurança

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Gerencie seus URIs de redirecionamento: <ul><li>Mantenha a propriedade de todos os URIs de redirecionamento e mantenha os registros DNS para eles atualizados.</li><li>Não use caracteres curinga (*) em seus URIs.</li><li>Para aplicativos Web, verifique se todos os URIs estão seguros e criptografados (por exemplo, usando esquemas HTTPS).</li><li>Para clientes públicos, use URIs de redirecionamento específicos da plataforma, se aplicável (principalmente para iOS e Android). Caso contrário, use URIs de redirecionamento com uma grande quantidade de aleatoriedade para evitar colisões ao chamar de volta para seu aplicativo.</li><li>Se seu aplicativo estiver sendo usado de um agente Web isolado, você poderá usar https://login.microsoftonline.com/common/oauth2/nativeclient o.</li><li>Revise e corte todos os URIs de redirecionamento não utilizados ou desnecessários regularmente.</li></ul> |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se seu aplicativo estiver registrado em um diretório, minimize e monitore manualmente a lista de proprietários de registro de aplicativo. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não habilite o suporte para o [fluxo de concessão implícita OAuth2](v2-oauth2-implicit-grant-flow.md) , a menos que seja explicitamente necessário. Saiba mais sobre o cenário válido [aqui](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Mover para além do nome de usuário/senha. Não use o [ROPC (fluxo de credenciais de senha) do proprietário do recurso](v2-oauth-ropc.md), que lida diretamente com as senhas dos usuários. Esse fluxo requer um alto grau de confiança e exposição do usuário e deve ser usado somente quando outros fluxos mais seguros não podem ser usados. Esse fluxo ainda é necessário em alguns cenários (como DevOps), mas lembre-se de que usá-lo irá impor restrições em seu aplicativo.  Para abordagens mais modernas, leia [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md).|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteja e gerencie suas credenciais de aplicativo confidenciais para aplicativos Web, APIs da Web e aplicativos de daemon. Use [credenciais de certificado](active-directory-certificate-credentials.md), não credenciais de senha (segredos de cliente). Se você precisar usar uma credencial de senha, não a defina manualmente. Não armazene credenciais no código ou na configuração e nunca permita que elas sejam manipuladas por seres humanos. Se possível, use [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para armazenar e girar suas credenciais regularmente. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Verifique se seu aplicativo solicita as permissões de privilégios mínimos. Solicite apenas permissões que seu aplicativo precisa, e somente quando você precisar deles. Entenda os diferentes [tipos de permissões](v1-permissions-and-consent.md#types-of-permissions). Use permissões de aplicativo somente se necessário; Use permissões delegadas sempre que possível. Para obter uma lista completa de permissões de Microsoft Graph, consulte esta [referência de permissões](https://docs.microsoft.com/graph/permissions-reference). |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se você estiver protegendo uma API usando a plataforma de identidade da Microsoft, pense cuidadosamente nas permissões que ele deve expor. Considere qual é a granularidade certa para sua solução e quais permissões exigem o consentimento do administrador. Verifique as permissões esperadas nos tokens de entrada antes de tomar decisões de autorização. |

## <a name="implementation"></a>Implementação

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Use as soluções de autenticação modernas (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) para conectar usuários com segurança. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Não programe diretamente contra protocolos como o OAuth 2,0 e o Open ID. Em vez disso, aproveite a [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md). As bibliotecas MSAL encapsulam com segurança protocolos de segurança em uma biblioteca fácil de usar e você obtém suporte interno para cenários de [acesso condicional](/azure/active-directory/conditional-access/overview) , [SSO (logon único)](/azure/active-directory/manage-apps/what-is-single-sign-on)de todo o dispositivo e suporte interno a cache de tokens. Para obter mais informações, consulte a lista de [bibliotecas de clientes](reference-v2-libraries.md#microsoft-supported-client-libraries) e [bibliotecas de middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) com suporte da Microsoft e a lista de bibliotecas de clientes de terceiros [compatíveis](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Se você precisar distribuir o código para os protocolos de autenticação, deverá seguir uma metodologia como o [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Preste muita atenção às considerações de segurança nas especificações de padrões para cada protocolo.|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migre aplicativos existentes da [Adal (biblioteca de autenticação Azure Active Directory)](active-directory-authentication-libraries.md) para a [biblioteca de autenticação da Microsoft](msal-overview.md). A MSAL é a mais recente solução de plataforma de identidade da Microsoft e é preferida à ADAL. Ele está disponível em .NET e JavaScript e também está em visualização pública para Android, iOS, Python e Java. Leia mais sobre como migrar os aplicativos [Adal.net](msal-net-migration.md), [Adal. js](msal-compare-msal-js-and-adal-js.md)e [Adal.net e Ios Broker](msal-net-migration-ios-broker.md) .|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Para aplicativos móveis, configure cada plataforma usando a experiência de registro do aplicativo. Para que seu aplicativo aproveite o Microsoft Authenticator ou o Microsoft Portal da Empresa para logon único, seu aplicativo precisa de um "URI de redirecionamento do Broker" configurado. Isso permite que a Microsoft retorne o controle ao seu aplicativo após a autenticação. Ao configurar cada plataforma, a experiência de registro do aplicativo orientará você pelo processo. Use o guia de início rápido para baixar um exemplo de trabalho. No iOS, use agentes e WebView do sistema sempre que possível.|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Em aplicativos Web ou APIs Web, mantenha um cache de token por conta.  Para aplicativos Web, o cache de token deve ser codificado pela ID da conta.  Para APIs Web, a conta deve ser codificada pelo hash do token usado para chamar a API. O MSAL.NET fornece a serialização de cache de token personalizada nas subplataformas .NET Framework e .NET Core. Por motivos de segurança e desempenho, nossa recomendação é serializar um cache por usuário. Para obter mais informações, leia sobre [serialização de cache de token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se os dados necessários para o seu aplicativo estiverem disponíveis por meio de [Microsoft Graph](https://developer.microsoft.com/graph), solicite permissões para esses dados usando o ponto de extremidade Microsoft Graph em vez da API individual. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |Não examine o valor do token de acesso ou tente analisá-lo como um cliente.  Eles podem alterar valores, formatos ou até mesmo serem criptografados sem aviso-sempre use o id_token se o cliente precisar aprender algo sobre o usuário ou chamar Microsoft Graph.  Somente as APIs da Web devem analisar os tokens de acesso (já que são aqueles que definem o formato e definindo as chaves de criptografia). |

## <a name="end-user-experience"></a>Experiência do utilizador final

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | [Entenda a experiência de consentimento](application-consent-experience.md) e configure as partes do prompt de consentimento do seu aplicativo para que os usuários finais e administradores tenham informações suficientes para determinar se eles confiam no seu aplicativo. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimize o número de vezes que um usuário precisa inserir credenciais de logon ao usar seu aplicativo tentando a autenticação silenciosa (aquisição de token silenciosa) antes dos fluxos interativos. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não use "prompt = consentimento" para cada entrada. Use avisar = consentimento somente se você tiver determinado que precisa pedir permissões adicionais (por exemplo, se tiver alterado as permissões necessárias do aplicativo). |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Quando aplicável, enriquecer seu aplicativo com os dados do usuário. Use a [API Microsoft Graph](https://developer.microsoft.com/graph) é uma maneira fácil de fazer isso. A ferramenta [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) que pode ajudá-lo a começar. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Registre o conjunto completo de permissões que seu aplicativo requer para que os administradores possam conceder o consentimento facilmente ao seu locatário. Use o [consentimento incremental](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) em tempo de execução para ajudar os usuários a entender por que seu aplicativo está solicitando permissões que podem se preocupar ou confundir os usuários quando solicitado na primeira inicialização. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Implemente uma [experiência de logoff único limpa](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Trata-se de uma privacidade e um requisito de segurança, e possibilita uma boa experiência do usuário. |

## <a name="testing"></a>Testes

|   |   |
|---|---|
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste para [políticas de acesso condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que podem afetar a capacidade dos usuários de usar seu aplicativo. |
| ![verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste seu aplicativo com todas as contas possíveis às quais você planeja dar suporte (por exemplo, contas corporativas ou de estudante, contas pessoais da Microsoft, contas filho e contas soberanas). |

## <a name="additional-resources"></a>Recursos adicionais

Explore informações aprofundadas sobre o v2.0:

* [Plataforma Microsoft Identity (visão geral do v 2.0)](v2-overview.md)
* [Referência de protocolos da plataforma Microsoft Identity](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento na plataforma Microsoft Identity](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
