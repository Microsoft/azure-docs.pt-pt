---
title: Boas práticas para a plataforma de identidade da Microsoft Azure
description: Conheça as melhores práticas, recomendações e descuidos comuns ao integrar-se com a plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050486"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>As melhores práticas e recomendações da plataforma de identidade microsoft

Este artigo destaca as melhores práticas, recomendações e descuidos comuns ao integrar-se com a plataforma de identidade da Microsoft.  Esta lista de verificação irá guiá-lo para uma integração de alta qualidade e segura. Reveja esta lista regularmente para garantir que mantém a qualidade e segurança da integração da sua aplicação com a plataforma de identidade. A lista de verificação não pretende rever toda a sua aplicação. Os conteúdos da lista de verificação estão sujeitos a alterações à medida que fazemos melhorias na plataforma.

Se está apenas a começar, consulte a [documentação](index.yml) da plataforma de identidade da Microsoft para saber sobre os fundamentos da autenticação, cenários de aplicação na plataforma de identidade da Microsoft e muito mais.

Utilize a seguinte lista de verificação para garantir que a sua aplicação está efetivamente integrada na [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Noções básicas

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Leia e compreenda as Políticas da [Plataforma Microsoft.](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409) Certifique-se de que a sua aplicação adere aos termos descritos, uma vez que são projetados para proteger os utilizadores e a plataforma. |

## <a name="ownership"></a>Propriedade

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Certifique-se de que as informações associadas à conta que usou para registar e gerir aplicações estão atualizadas. |

## <a name="branding"></a>Imagem corporativa

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Respeite as diretrizes de [Branding para aplicações](howto-add-branding-in-azure-ad-apps.md). |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça um nome e logotipo significativos para a sua aplicação. Estas informações aparecem no pedido de consentimento do seu pedido de [consentimento.](application-consent-experience.md) Certifique-se de que o seu nome e logotipo são representativos da sua empresa/produto para que os utilizadores possam tomar decisões informadas. Certifique-se de que não está a violar nenhuma marca registada. |

## <a name="privacy"></a>Privacidade

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça links para os termos de serviço e declaração de privacidade da sua aplicação. |

## <a name="security"></a>Segurança

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Gerencie os seus URIs redirecionados: <ul><li>Mantenha a propriedade de todos os seus URIs redirecionados e mantenha os registos DNS para os mesmos atualizados.</li><li>Não utilize wildcards (*) nas suas URIs.</li><li>Para aplicações web, certifique-se de que todos os URIs são seguros e encriptados (por exemplo, utilizando esquemas https).</li><li>Para clientes públicos, utilize URIs redirecionados específicos da plataforma, se aplicável (principalmente para iOS e Android). Caso contrário, utilize URIs redirecionamento com uma elevada quantidade de aleatoriedade para evitar colisões quando ligar de volta para a sua aplicação.</li><li>Se a sua aplicação estiver a ser utilizada `https://login.microsoftonline.com/common/oauth2/nativeclient`a partir de um agente web isolado, poderá utilizar .</li><li>Reveja e corte regularmente todas as URIs não utilizadas ou desnecessárias.</li></ul> |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se a sua aplicação estiver registada num diretório, minimize e monitorize manualmente a lista dos proprietários de registos de aplicações. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não permita o apoio ao [fluxo de subvenção implícito OAuth2](v2-oauth2-implicit-grant-flow.md) a menos que seja expressamente necessário. Conheça o cenário válido [aqui](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Mova-se para além do nome de utilizador/palavra-passe. Não utilize o fluxo credencial de senha do proprietário de [recursos (ROPC)](v2-oauth-ropc.md), que lida diretamente com as palavras-passe dos utilizadores. Este fluxo requer um elevado grau de confiança e exposição ao utilizador e só deve ser utilizado quando outros fluxos mais seguros não podem ser utilizados. Este fluxo ainda é necessário em alguns cenários (como devOps), mas tenha cuidado com o facto de a sua utilização impor constrangimentos à sua aplicação.  Para abordagens mais modernas, leia [fluxos de autenticação e cenários de aplicação.](authentication-flows-app-scenarios.md)|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteja e gere as suas credenciais confidenciais de aplicações para aplicações web, APIs web e aplicações daemon. Utilize credenciais de [certificado,](active-directory-certificate-credentials.md)não credenciais de senha (segredos do cliente). Se tiver de utilizar uma credencial de senha, não a derre manualmente. Não guarde credenciais em código ou config, e nunca permita que sejam manuseadas por humanos. Se possível, utilize [identidades geridas para os recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para armazenar e rodar regularmente as suas credenciais. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Certifique-se de que o seu pedido solicita as permissões de menor privilégio. Só peça permissões de que a sua aplicação precisa absolutamente, e apenas quando precisar delas. Compreenda os [diferentes tipos de permissões.](v2-permissions-and-consent.md#permission-types) Utilize apenas permissões de pedido se necessário; utilizar permissões delegadas sempre que possível. Para obter uma lista completa das permissões do Microsoft Graph, consulte esta referência de [permissões](https://docs.microsoft.com/graph/permissions-reference). |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se estiver a assegurar uma API utilizando a plataforma de identidade da Microsoft, pense cuidadosamente através das permissões que deve expor. Considere qual é a granularidade certa para a sua solução e que permissão(s) requer consentimento administrativo. Verifique as permissões esperadas nos tokens de entrada antes de tomar quaisquer decisões de autorização. |

## <a name="implementation"></a>Implementação

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Utilize soluções modernas de autenticação (OAuth 2.0, [OpenID Connect)](v2-protocols-oidc.md)para iniciar sessão de forma segura nos utilizadores. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Não programe diretamente contra protocolos como OAuth 2.0 e Open ID. Em vez disso, aproveite a Biblioteca de Autenticação da [Microsoft (MSAL)](msal-overview.md). As bibliotecas MSAL embrulham seguramente protocolos de segurança numa biblioteca de fácil utilização, e obtém suporte incorporado para cenários de [Acesso Condicional,](/azure/active-directory/conditional-access/overview) inscrição única em todo o dispositivo [(SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)e suporte de cache em butida incorporado. Para mais informações, consulte a lista de [bibliotecas](reference-v2-libraries.md#microsoft-supported-client-libraries) de clientes suportadas pela Microsoft e [bibliotecas de middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) e a lista de bibliotecas de [clientes compatíveis de terceiros.](reference-v2-libraries.md#compatible-client-libraries)<br/><br/>Se tiver de entregar código para os protocolos de autenticação, deverá seguir uma metodologia como o [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Preste muita atenção às considerações de segurança nas especificações de normas para cada protocolo.|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migrar aplicativos existentes da Biblioteca de [Autenticação de Diretórios Ativos do Azure (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) para a [Microsoft Authentication Library](msal-overview.md). A MSAL é a mais recente solução de plataforma de identidade da Microsoft e é preferida para a ADAL. Está disponível em .NET, JavaScript, Android, iOS, macOS e também está em pré-visualização pública para Python e Java. Leia mais sobre [ADAL.NET](msal-net-migration.md)migratórios , [ADAL.js](msal-compare-msal-js-and-adal-js.md), e [aplicações de corretor ADAL.NET e iOS.](msal-net-migration-ios-broker.md)|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Para aplicações móveis, configure cada plataforma utilizando a experiência de registo de aplicações. Para que a sua aplicação aproveite o Microsoft Authenticator ou o Portal da Microsoft Company para um único sessão, a sua aplicação necessita de um "broker redirecionamento URI" configurado. Isto permite que a Microsoft devolva o controlo da sua aplicação após a autenticação. Ao configurar cada plataforma, a experiência de registo da aplicação irá guiá-lo através do processo. Use o quickstart para descarregar um exemplo de funcionamento. No iOS, utilize corretores e webview do sistema sempre que possível.|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |  Nas aplicações web ou nas APIs web, mantenha uma cache simbólica por conta.  Para aplicações web, o cache token deve ser chave da conta ID.  Para apis web, a conta deve ser chaveada pelo hash do token usado para chamar a API. MSAL.NET fornece a serialização de cache token personalizada nas subplataformas .NET Framework e .NET Core. Por razões de segurança e desempenho, a nossa recomendação é serializar uma cache por utilizador. Para mais informações, leia sobre a serialização da [cache simbólica.](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se os dados que a sua aplicação necessitar estiver em função do [Microsoft Graph,](https://developer.microsoft.com/graph)solicite permissões para estes dados utilizando o ponto final do Microsoft Graph em vez do API individual. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) |Não olhe para o valor do token de acesso, ou tente analisá-lo como um cliente.  Podem alterar valores, formatos ou até mesmo ficar encriptados sem aviso - use sempre o id_token se o seu cliente precisar de aprender algo sobre o utilizador, ou ligar para o Microsoft Graph.  Apenas as APIs web devem analisar fichas de acesso (uma vez que são as que definem o formato e configuram as teclas de encriptação). |

## <a name="end-user-experience"></a>Experiência do utilizador final

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | [Compreenda a experiência](application-consent-experience.md) de consentimento e configure as peças do consentimento da sua aplicação para que os utilizadores finais e administradores tenham informações suficientes para determinar se confiam na sua aplicação. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimize o número de vezes que um utilizador precisa de introduzir credenciais de login enquanto utiliza a sua aplicação, tentando autenticação silenciosa (aquisição de token silencioso) antes de fluxos interativos. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não utilize "prompt=consent" para cada inscrição. Utilize apenas o pedido=consentimento se tiver determinado que precisa de pedir consentimento para permissões adicionais (por exemplo, se tiver alterado as permissões necessárias da sua aplicação). |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Quando aplicável, enriqueça a sua aplicação com os dados do utilizador. Usar a [API](https://developer.microsoft.com/graph) do Microsoft Graph é uma maneira fácil de fazer isto. A ferramenta [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) que pode ajudá-lo a começar. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Registe o conjunto completo de permissões que a sua aplicação necessita para que os administradores possam conceder o consentimento facilmente ao seu inquilino. Utilize [o consentimento incremental](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) no momento do funcionamento para ajudar os utilizadores a entender porque é que a sua aplicação está a solicitar permissões que podem preocupar ou confundir os utilizadores quando solicitados no início. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementar uma [experiência de inscrição simples e limpa.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) É uma privacidade e um requisito de segurança, e faz com que seja uma boa experiência do utilizador. |

## <a name="testing"></a>Testar

|   |   |
|---|---|
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste para políticas de [acesso condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que possam afetar a capacidade dos seus utilizadores de utilizarem a sua aplicação. |
| ![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste a sua aplicação com todas as contas possíveis que pretende suportar (por exemplo, contas de trabalho ou de escola, contas pessoais da Microsoft, contas de crianças e contas soberanas). |

## <a name="additional-resources"></a>Recursos adicionais

Explore informações aprofundadas sobre o v2.0:

* [Plataforma de identidade da Microsoft (visão geral v2.0)](v2-overview.md)
* [Referência de protocolos de plataforma de identidade da Microsoft](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
