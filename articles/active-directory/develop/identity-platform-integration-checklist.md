---
title: Boas práticas para a plataforma de identidade da Microsoft Rio Azure
description: Conheça as melhores práticas, recomendações e descuidos comuns ao integrar-se na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 94da22fbb2940bddaad1324bedc8759706eca37b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173571"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>As melhores práticas e recomendações da plataforma de identidade da Microsoft

Este artigo destaca as melhores práticas, recomendações e descuidos comuns ao integrar-se na plataforma de identidade da Microsoft.  Esta lista de verificação irá guiá-lo para uma integração de alta qualidade e segurança. Reveja esta lista regularmente para garantir que mantém a qualidade e segurança da integração da sua aplicação com a plataforma de identidade. A lista de verificação não se destina a rever toda a sua aplicação. Os conteúdos da lista de verificação estão sujeitos a alterações à medida que fazemos melhorias na plataforma.

Se está apenas a começar, consulte a [documentação](index.yml) da plataforma de identidade da Microsoft para saber mais sobre os fundamentos de autenticação, cenários de aplicações na plataforma de identidade da Microsoft e muito mais.

Utilize a seguinte lista de verificação para garantir que a sua aplicação está efetivamente integrada na plataforma de identidade da [Microsoft.](./index.yml)

> [!TIP]
> O *assistente de integração* no portal Azure pode ajudá-lo a aplicar muitas destas boas práticas e recomendações. Selecione qualquer uma das suas inscrições de [aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal Azure e, em seguida, selecione o item do menu **do assistente de Integração (pré-visualização)** para começar com o assistente.

## <a name="basics"></a>Noções básicas

![checkbox ](./media/active-directory-integration-checklist/checkbox-two.svg) Leia e compreenda as Políticas da Plataforma [microsoft](/legal/microsoft-identity-platform/terms-of-use). Certifique-se de que a sua aplicação adere aos termos delineados, uma vez que foram concebidos para proteger os utilizadores e a plataforma.

## <a name="ownership"></a>Propriedade

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Certifique-se de que as informações associadas à conta utilizada para registar e gerir aplicações estão atualizadas.

## <a name="branding"></a>Imagem corporativa

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Aderir às [diretrizes de branding para aplicações](howto-add-branding-in-azure-ad-apps.md).

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Forneça um nome e logotipo significativos para a sua aplicação. Esta informação aparece no pedido de consentimento do seu [pedido.](application-consent-experience.md) Certifique-se de que o seu nome e logotipo são representativos da sua empresa/produto para que os utilizadores possam tomar decisões informadas. Certifique-se de que não está a violar nenhuma marca registada.

## <a name="privacy"></a>Privacidade

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Forneça links para os termos de serviço e declaração de privacidade da sua aplicação.

## <a name="security"></a>Segurança

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Gerir os uris de redirecionamento: <ul><li>Mantenha a propriedade de todos os seus URIs redirecionados e mantenha os registos DNS para os mesmos atualizados.</li><li>Não utilize wildcards (*) nos seus URIs.</li><li>Para aplicações web, certifique-se de que todos os URIs estão seguros e encriptados (por exemplo, utilizando esquemas https).</li><li>Para clientes públicos, utilize URIs de redirecionamento específico da plataforma, se aplicável (principalmente para iOS e Android). Caso contrário, utilize URIs redirecionados com uma elevada quantidade de aleatoriedade para evitar colisões quando voltar a ligar para a sua aplicação.</li><li>Se a sua aplicação estiver a ser utilizada a partir de um agente web isolado, poderá `https://login.microsoftonline.com/common/oauth2/nativeclient` utilizar.</li><li>Reveja e corte todos os URIs de redirecionamento não reutilizados ou desnecessários numa base regular.</li></ul>

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Se a sua aplicação estiver registada num diretório, minimize e monitorize manualmente a lista de proprietários de registos de aplicações.

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Não permita suporte para o fluxo de [subvenção implícita OAuth2,](v2-oauth2-implicit-grant-flow.md) a menos que seja explicitamente necessário. Conheça o cenário válido [aqui.](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Mover-se para além do nome de utilizador/palavra-passe. Não utilize [o fluxo de credencial de senha do proprietário de recursos (ROPC),](v2-oauth-ropc.md)que trata diretamente as palavras-passe dos utilizadores. Este fluxo requer um alto grau de confiança e exposição do utilizador e só deve ser utilizado quando outros fluxos, mais seguros, não puderem ser utilizados. Este fluxo ainda é necessário em alguns cenários (como DevOps), mas tenha cuidado que usá-lo irá impor restrições na sua aplicação.  Para abordagens mais modernas, leia [fluxos de autenticação e cenários de aplicação.](authentication-flows-app-scenarios.md)

![checkbox ](./media/active-directory-integration-checklist/checkbox-two.svg) Proteja e gere as suas credenciais confidenciais de aplicações para aplicações web, APIs web e aplicações daemon. Use [credenciais de certificado,](active-directory-certificate-credentials.md)não credenciais de senha (segredos do cliente). Se tiver de usar uma credencial de senha, não a desloque manualmente. Não guarde credenciais em código ou config, e nunca permita que sejam tratadas por humanos. Se possível, utilize [identidades geridas para recursos Azure](../managed-identities-azure-resources/overview.md) ou [Azure Key Vault](../../key-vault/general/basic-concepts.md) para armazenar e rodar regularmente as suas credenciais.

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Certifique-se de que a sua aplicação solicita as permissões de menor privilégio. Apenas peça permissões que a sua aplicação precisa absolutamente, e apenas quando precisar delas. Compreenda os [diferentes tipos de permissões.](v2-permissions-and-consent.md#permission-types) Utilize apenas permissões de aplicação se necessário; utilizar permissões delegadas sempre que possível. Para obter uma lista completa das permissões do Microsoft Graph, consulte esta [referência de permissões](/graph/permissions-reference).

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Se estiver a proteger uma API utilizando a plataforma de identidade da Microsoft, pense cuidadosamente através das permissões que deve expor. Considere qual é a granularidade certa para a sua solução e qual a permissão(s) que requer consentimento administrativo. Verifique se as permissões esperadas nos tokens de entrada antes de tomar quaisquer decisões de autorização.

## <a name="implementation"></a>Implementação

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Utilize soluções de autenticação modernas (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) para iniciar um sinal seguro nos utilizadores.

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Não programe diretamente contra protocolos como OAuth 2.0 e Open ID. Em vez disso, aproveite a [Biblioteca de Autenticação da Microsoft (MSAL)](msal-overview.md). As bibliotecas MSAL envolvem de forma segura protocolos de segurança numa biblioteca de fácil utilização, e obtém suporte incorporado para cenários [de acesso condicional,](../conditional-access/overview.md) sinalização única em todo o dispositivo [(SSO)](../manage-apps/what-is-single-sign-on.md)e suporte de caching em token incorporado. Para obter mais informações, consulte a lista de bibliotecas de [clientes](reference-v2-libraries.md#microsoft-supported-client-libraries) e [bibliotecas](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) de middleware suportadas pela Microsoft e a lista de [bibliotecas de clientes de terceiros compatíveis.](reference-v2-libraries.md#compatible-client-libraries)<br/><br/>Se tiver de entregar código para os protocolos de autenticação, deve seguir uma metodologia como [o Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Preste muita atenção às considerações de segurança nas especificações de normas de cada protocolo.

![checkbox ](./media/active-directory-integration-checklist/checkbox-two.svg) Migrar aplicações existentes da [Azure Ative Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) para a [Microsoft Authentication Library](msal-overview.md). A MSAL é a mais recente solução de plataforma de identidade da Microsoft e é preferida à ADAL. Está disponível em .NET, JavaScript, Android, iOS, macOS e também está em pré-visualização pública para Python e Java. Leia mais sobre [ADAL.NET](msal-net-migration.md)migração , [ADAL.js](msal-compare-msal-js-and-adal-js.md), e [aplicações de corretores de ADAL.NET e iOS.](msal-net-migration-ios-broker.md)

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Para aplicações móveis, configurar cada plataforma utilizando a experiência de registo de aplicações. Para que a sua aplicação possa tirar partido do Microsoft Authenticator ou do Microsoft Company Portal para uma única sinteção, a sua aplicação necessita de um "broker redirect URI" configurado. Isto permite que a Microsoft devolva o controlo à sua aplicação após a autenticação. Ao configurar cada plataforma, a experiência de registo de aplicações irá guiá-lo através do processo. Use o quickstart para descarregar um exemplo de trabalho. No iOS, utilize corretores e webview do sistema sempre que possível.

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Em aplicativos web ou APIs web, mantenha uma cache simbólica por conta.  Para aplicações web, a cache simbólica deve ser chaveda pelo ID da conta.  Para as APIs web, a conta deve ser chaveizada pelo haxixe do símbolo usado para chamar a API. MSAL.NET fornece serialização de cache de fichas personalizadas nas subplatas .NET e .NET Core. Por razões de segurança e desempenho, a nossa recomendação é serializar uma cache por utilizador. Para mais informações, leia sobre [a serialização da cache](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)simbólica.

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Se os dados que a sua aplicação necessita estiverem disponíveis através do [Microsoft Graph,](https://developer.microsoft.com/graph)solicite permissões para estes dados utilizando o ponto final do Microsoft Graph em vez da API individual.

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Não olhe para o valor do símbolo de acesso, ou tente analisá-lo como um cliente.  Podem alterar valores, formatos ou até mesmo ficar encriptados sem aviso prévio - utilize sempre o id_token se o seu cliente precisar de aprender algo sobre o utilizador, ou ligue para o Microsoft Graph.  Apenas as APIs web devem analisar os tokens de acesso (uma vez que são os que definem o formato e definem as teclas de encriptação).

## <a name="end-user-experience"></a>Experiência do utilizador final

![checkbox ](./media/active-directory-integration-checklist/checkbox-two.svg) [Compreenda a experiência de consentimento](application-consent-experience.md) e configuure as peças da solicitação de consentimento da sua aplicação para que os utilizadores finais e administradores tenham informações suficientes para determinar se confiam na sua app.

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Minimize o número de vezes que um utilizador necessita para introduzir credenciais de login enquanto utiliza a sua aplicação, tentando a autenticação silenciosa (aquisição de token silencioso) antes de fluxos interativos.

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Não utilize "prompt=consent" para cada s indicado. Utilize apenas o pedido de consentimento se tiver determinado que precisa de pedir consentimento para permissões adicionais (por exemplo, se tiver alterado as permissões necessárias da sua aplicação).

![caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) Sempre que aplicável, enriqueça a sua aplicação com os dados do utilizador. A utilização da [API do Gráfico microsoft](https://developer.microsoft.com/graph) é uma forma fácil de o fazer. A ferramenta [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) que pode ajudá-lo a começar.

![checkbox ](./media/active-directory-integration-checklist/checkbox-two.svg) Registe o conjunto completo de permissões que a sua aplicação requer para que os administradores possam conceder o consentimento facilmente ao seu inquilino. Utilize [o consentimento incremental](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) no tempo de execução para ajudar os utilizadores a entender por que a sua aplicação está a solicitar permissões que podem preocupar ou confundir utilizadores quando solicitado no primeiro começo.

![caixa de ](./media/active-directory-integration-checklist/checkbox-two.svg) verificação Implementar uma [experiência de sinalização única limpa](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). É uma privacidade e um requisito de segurança, e faz com que seja uma boa experiência do utilizador.

## <a name="testing"></a>Testar

![teste de caixa de verificação ](./media/active-directory-integration-checklist/checkbox-two.svg) para políticas de acesso [condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que possam afetar a capacidade de utilização da sua aplicação pelos seus utilizadores.

![caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) Teste a sua aplicação com todas as contas possíveis que pretende suportar (por exemplo, contas de trabalho ou escola, contas pessoais da Microsoft, contas de crianças e contas soberanas).

## <a name="additional-resources"></a>Recursos adicionais

Explore informações aprofundadas sobre o v2.0:

* [Plataforma de identidade da Microsoft (visão geral v2.0)](v2-overview.md)
* [Referência de protocolos de plataforma de identidade da Microsoft](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)