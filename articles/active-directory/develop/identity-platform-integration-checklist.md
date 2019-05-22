---
title: Integrar com a plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre as práticas recomendadas e oversights comuns ao integrar com a plataforma de identidade da Microsoft (v2.0).
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: e9070127780659142ab8f956a8016622ecfea144
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540171"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Lista de verificação do Microsoft identity platform integração

A lista de verificação de integração de plataforma de identidade do Microsoft destina-se para orientá-lo para uma integração de alta qualidade e segura. Ele destaca as práticas recomendadas e oversights comuns ao integrar com a plataforma de identidade da Microsoft por isso, reveja a lista em intervalos regulares para se certificar-se de que manter a qualidade e segurança de integração da sua aplicação com a plataforma de identidade. A lista de verificação não foi concebida para revisar todo o seu aplicativo. O conteúdo da lista de verificação está sujeitos a alterações à medida que melhoramos para a plataforma.

Se estiver apenas começando, veja a [documentação](index.yml) para saber mais sobre as noções básicas de autenticação, cenários de aplicativos na plataforma de identidade da Microsoft e muito mais.

## <a name="testing-your-integration"></a>A integração do teste

Utilize a lista de verificação seguinte para se certificar de que seu aplicativo com eficiência é integrado com o [plataforma de identidade do Microsoft](https://docs.microsoft.com/legal/mdsa).

### <a name="basics"></a>Noções Básicas

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Leia e compreenda o [políticas de plataforma da Microsoft](https://docs.microsoft.com/legal/mdsa). Certifique-se de que seu aplicativo respeite os termos descritos como foram concebidos para proteger os utilizadores e a plataforma. |

### <a name="ownership"></a>Propriedade

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Certifique-se de que as informações associadas à conta que utilizou para se registrar e gerir aplicações estão atualizadas. |

### <a name="branding"></a>Personalização

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Seguir a [diretrizes para aplicativos de imagem corporativa](howto-add-branding-in-azure-ad-apps.md). |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça um nome significativo e o logótipo para a sua aplicação. Esta informação é apresentada na solicitação de consentimento da sua aplicação. Certificar-se de que o seu nome e logótipo são representativos de seu produto/da empresa para que os usuários podem tomar decisões informadas. Certifique-se de que não está a violação quaisquer marcas registadas. |

### <a name="privacy"></a>Privacidade

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornece ligações para os termos da sua aplicação de serviço e declaração de privacidade. |

### <a name="security"></a>Segurança

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Manter a propriedade de todos os seus URIs de redirecionamento e manter atualizados os registos DNS para os mesmos. Não utilize carateres universais (*) nos seus URIs. Para aplicações web, certifique-se de que todos os URIs são seguras e encriptadas (por exemplo, usando os esquemas de https). Para os clientes públicos, utilize URIs de redirecionamento de específicas da plataforma, se aplicável (principalmente para iOS e Android). Caso contrário, utilize URIs de redirecionamento com uma elevada quantidade de aleatoriedade para impedir colisões quando retornos de chamada para a sua aplicação. Se seu aplicativo estiver sendo usado por um agente web isolado, pode usar https://login.microsoftonline.com/nativeclient. Reveja e cortar todos os não utilizados e desnecessários URIs de redirecionamento em intervalos regulares. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se a sua aplicação for registada num diretório, minimizar a e monitorizar manualmente a lista de proprietários do registo de aplicação. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não ativar o suporte para o [fluxo de concessão de OAuth2 implícita](v2-oauth2-implicit-grant-flow.md) , a menos que explicitamente necessárias. Saiba mais sobre o cenário válido [aqui](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não utilize [o fluxo de credenciais de palavra-passe do recurso proprietário (ROPC)](v2-oauth-ropc.md), que processa diretamente as de palavras-passe dos utilizadores. Este fluxo requer um alto grau de exposição de utilizador e de confiança e só deve ser utilizado quando os fluxos de outros, mais seguros, não podem ser utilizados. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteger e gerir as credenciais da sua aplicação. Uso [credenciais de certificado](active-directory-certificate-credentials.md), não credenciais de palavra-passe (segredos do cliente). Se tiver de utilizar uma credencial de palavra-passe, não defina-o manualmente. Não armazenar as credenciais na configuração ou de código e nunca permitir que os mesmos ser tratada por seres humanos. Se possível, utilize [geridos identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para armazenar e girar regularmente as suas credenciais. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Certifique-se de que a aplicação solicite as permissões de privilégio mínimo. Apenas pedir permissões que seu aplicativo precisa e apenas quando precisar delas. Compreender os diferentes [tipos de permissões](v1-permissions-and-consent.md#types-of-permissions). Utilize apenas permissões de aplicação, se necessário. Utilize permissões delegadas sempre que possível. Para obter uma lista completa de permissões do Microsoft Graph, consulte esta [referência de permissões](https://docs.microsoft.com/graph/permissions-reference). |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se estiver a proteger uma API com a plataforma de identidade da Microsoft, considere cuidadosamente as permissões que deve ser exposto. Considere o que é a granularidade correta para a sua solução e quais permissões requerem consentimento de administrador. Verifique as permissões esperadas nos tokens de entrada antes de tomar qualquer decisão de autorização. |

### <a name="implementation"></a>Implementação

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Utilize soluções de autenticação moderna (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) com segurança iniciar sessão dos utilizadores. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não implementar os protocolos de si mesmo – utilize [bibliotecas de autenticação suportada do Microsoft](reference-v2-libraries.md) (MSAL, middleware de servidor). Certifique-se de que está a utilizar a versão mais recente da biblioteca de autenticação que já integrado com. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Se a sua aplicação necessita de dados estão disponíveis através dos [Microsoft Graph](https://developer.microsoft.com/graph), solicitar permissões para estes dados utilizando o ponto de extremidade do Microsoft Graph em vez da API individual. |

### <a name="end-user-experience"></a>Experiência do utilizador final

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | [Compreender a experiência de consentimento](application-consent-experience.md) e configurar as partes do pedido de consentimento da sua aplicação para que os utilizadores finais e os administradores tenham informações suficientes para determinar se eles confiam a sua aplicação. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimizar o número de vezes que um usuário precisa inserir as credenciais de início de sessão ao utilizar a sua aplicação ao tentar silenciosa autenticação (aquisição do token silenciosa) antes de fluxos interativos. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Não utilize "pedido = consentimento" para cada início de sessão. Utilize apenas a linha de comandos = consentimento, se determinar que tem de pedir consentimento para pedir permissões adicionais (por exemplo, se tiver alterado as permissões necessárias da sua aplicação). |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Quando aplicável, enriquecer seu aplicativo com dados de utilizador. Utilize o [Microsoft Graph API](https://developer.microsoft.com/graph) é uma forma fácil de fazê-lo. O [API do Graph](https://developer.microsoft.com/graph/graph-explorer) ferramenta que pode ajudá-lo a começar a utilizar. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Registre-se o conjunto completo de permissões que a sua aplicação necessita para que os administradores podem conceder autorização facilmente ao seu inquilino. Uso [consentimento incremental](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) em tempo de execução para ajudar os utilizadores a compreender por que a aplicação está a pedir permissões que podem se ou confundir os usuários quando solicitada na primeira inicialização. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementar um [limpar a experiência de início de sessão único](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Ele é um requisito de segurança de privacidade e torna-se para uma boa experiência de utilizador. |

### <a name="testing"></a>A testar

|   |   |
|---|---|
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Testar [políticas de acesso condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que podem afetar a capacidade dos seus utilizadores para utilizar a sua aplicação. |
| ![Caixa de verificação](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste a sua aplicação com todas as contas de possíveis que planeia suportar (por exemplo, trabalho ou escola contas, contas Microsoft pessoais, contas de criança e garante a soberania: contas). |

## <a name="additional-resources"></a>Recursos adicionais

Explore informações aprofundadas sobre o v2.0:

* [Plataforma de identidade da Microsoft (descrição geral de v2.0)](v2-overview.md)
* [Referência de protocolos de plataforma de identidade Microsoft](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento no plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)