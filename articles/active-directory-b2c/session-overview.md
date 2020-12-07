---
title: SSO sessões em Azure Ative Directy B2C / Microsoft Docs
description: Uma visão geral do comportamento da sessão configurada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e02323df3a12c4a74de1fb62b36762fc739e9e5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750446"
---
# <a name="azure-ad-b2c-session"></a>Sessão Azure AD B2C

O sign-on único (SSO) adiciona segurança e comodidade quando os utilizadores assinam em todas as aplicações no Azure Ative Directory B2C (Azure AD B2C). Este artigo descreve os métodos de inscrição únicos utilizados no Azure AD B2C e ajuda-o a escolher o método SSO mais adequado ao configurar a sua política.

Com uma única súmis, os utilizadores insinuam-se uma vez com uma única conta e têm acesso a várias aplicações. A aplicação pode ser uma aplicação web, móvel ou de página única, independentemente do nome de plataforma ou de domínio.

Quando o utilizador inicialmente assina uma aplicação, o Azure AD B2C persiste numa sessão baseada em cookies. Após pedidos de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookies, e emite um token de acesso sem levar o utilizador a iniciar novamente a sessão. Se a sessão baseada em cookies expirar ou se tornar inválida, o utilizador é solicitado a iniciar novamente a sessão.  

## <a name="sso-session-types"></a>Tipos de sessão SSO

A integração com o Azure AD B2C envolve três tipos de sessões SSO:

- **Azure AD B2C** - Sessão gerida por Azure AD B2C
- **Fornecedor de identidade federada** - Sessão gerida pelo fornecedor de identidade, por exemplo Facebook, Salesforce ou conta Microsoft
- **Aplicação** - Sessão gerida pela aplicação web, móvel ou de página única

![Sessão de SSO](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

Quando um utilizador autentica com sucesso uma conta local ou social, o Azure AD B2C armazena uma sessão baseada em cookies no navegador do utilizador. O cookie é armazenado sob o nome de domínio do inquilino Azure AD B2C, tais como `https://contoso.b2clogin.com` .

Se um utilizador inicialmente assinar com uma conta federada e, em seguida, durante a janela do tempo de sessão (tempo de vida, ou TTL) assinar na mesma app ou numa aplicação diferente, o Azure AD B2C tenta adquirir um novo token de acesso ao fornecedor de identidade federado. Se a sessão de fornecedor de identidade federada for expirada ou inválida, o fornecedor de identidade federado solicita ao utilizador as suas credenciais. Se a sessão ainda estiver ativa (ou se o utilizador tiver assinado com uma conta local em vez de uma conta federada), o Azure AD B2C autoriza o utilizador e elimina mais solicitações.

Pode configurar o comportamento da sessão, incluindo a sessão TTL e como o Azure AD B2C partilha a sessão entre políticas e aplicações.

### <a name="federated-identity-provider-sso"></a>Fornecedor de identidade federada SSO

Um prestador de identidade social ou empresarial gere a sua própria sessão. O cookie é armazenado sob o nome de domínio do fornecedor de identidade, tais como `https://login.salesforce.com` . A Azure AD B2C não controla a sessão de fornecedor de identidade federada. Em vez disso, o comportamento da sessão é determinado pelo fornecedor de identidade federado. 

Considere o seguinte cenário:

1. Um utilizador assina no Facebook para verificar o seu feed.
2. Mais tarde, o utilizador abre a sua aplicação e inicia o processo de início de sação. A aplicação redireciona o utilizador para Azure AD B2C para completar o processo de inscrição.
3. Na página de inscrição ou inscrição do Azure AD B2C, o utilizador opta por iniciar seduca com a sua conta de Facebook. O utilizador é redirecionado para o Facebook. Se houver uma sessão ativa no Facebook, o utilizador não é solicitado a fornecer as suas credenciais e é imediatamente redirecionado para Azure AD B2C com um token do Facebook.

### <a name="application-sso"></a>SSO de aplicação

Uma aplicação web, móvel ou de página única pode ser protegida por acesso OAuth, fichas de ID ou fichas SAML. Quando um utilizador tenta aceder a um recurso protegido na aplicação, a aplicação verifica se existe uma sessão ativa no lado da aplicação. Se não houver sessão de aplicações ou a sessão tiver expirado, a aplicação levará o utilizador ao Azure AD B2C para a página de inscrição.

A sessão de aplicação pode ser uma sessão baseada em cookies armazenada sob o nome de domínio da aplicação, como `https://contoso.com` . As aplicações móveis podem armazenar a sessão de uma forma diferente, mas usando uma abordagem semelhante.

## <a name="azure-ad-b2c-session-configuration"></a>Configuração da sessão Azure AD B2C

### <a name="session-scope"></a>Âmbito de sessão

A sessão Azure AD B2C pode ser configurada com os seguintes âmbitos:

- **Inquilino** - Esta definição é o padrão. A utilização desta definição permite que várias aplicações e fluxos de utilizador no seu inquilino B2C partilhem a mesma sessão de utilizador. Por exemplo, uma vez que um utilizador assina uma aplicação, o utilizador também pode assinar perfeitamente em outra ao aceder à sua aplicação.
- **Aplicação** - Esta definição permite manter uma sessão de utilizador exclusivamente para uma aplicação, independente de outras aplicações. Por exemplo, pode utilizar esta definição se quiser que o utilizador faça o sôm.
- **Política** - Esta definição permite manter uma sessão de utilizador exclusivamente para um fluxo de utilizador, independente das aplicações que a utilizam. Por exemplo, se o utilizador já tiver assinado e concluído um passo de autenticação multi-factor (MFA), o utilizador pode ter acesso a partes de segurança superior de várias aplicações, desde que a sessão ligada ao fluxo do utilizador não expire.
- **Desativado** - Esta definição obriga o utilizador a executar todo o fluxo do utilizador em cada execução da apólice.

### <a name="session-life-time"></a>Tempo de vida da sessão

O tempo de vida da **sessão** é a quantidade de tempo que o cookie de sessão Azure AD B2C é armazenado no navegador do utilizador após a autenticação bem sucedida. Pode definir o tempo de vida da sessão para um valor de 15 a 720 minutos.

### <a name="keep-me-signed-in"></a>Manter a sessão iniciada

O recurso [Keep me signed-in](custom-policy-keep-me-signed-in.md) prolonga o tempo de vida da sessão através do uso de um cookie persistente. A sessão permanece ativa após o fecho do utilizador e reabre o navegador. A sessão só é revogada quando um utilizador assina. O recurso Keep me signed-in só se aplica ao sin-in com contas locais.

O recurso Keep me signed-in tem precedência sobre o tempo de vida da sessão. Se a função Desinsessão keep me estiver ativada e o utilizador o selecionar, esta funcionalidade dita quando a sessão expirará. 

### <a name="session-expiry-type"></a>Tipo de expiração da sessão

O **tipo de expiração da sessão** indica como uma sessão é prolongada pela definição do tempo de vida da sessão ou pela definição de me manter inscrito.

- **Rolar** - Indica que a sessão é prolongada sempre que o utilizador realiza uma autenticação baseada em cookies (predefinição).
- **Absoluto** - Indica que o utilizador é obrigado a reautensar após o período de tempo especificado.

## <a name="sign-out"></a>Terminar sessão

Quando pretender assinar o utilizador fora da aplicação, não basta limpar os cookies da aplicação ou terminar a sessão com o utilizador. Tem de redirecionar o utilizador para Azure AD B2C para se inscrever. Caso contrário, o utilizador poderá voltar a autenticar as suas aplicações sem voltar a introduzir as suas credenciais.

Após um pedido de sinalização, Azure AD B2C:

1. Invalida a sessão baseada em cookies Azure AD B2C.
1. Tentativas de assinatura de fornecedores de identidade federados:
   - OpenId Connect - Se o ponto final de configuração bem conhecido do fornecedor de identidade especificar uma `end_session_endpoint` localização.
   - OAuth2 - Se os [metadados do fornecedor de identidade](oauth2-technical-profile.md#metadata) contiver a `end_session_endpoint` localização.
   - SAML - Se os [metadados do fornecedor de identidade](saml-identity-provider-technical-profile.md#metadata) contiver a `SingleLogoutService` localização.
1. Opcionalmente, o sinal de saída de outras aplicações. Para mais informações, consulte a secção [de sinalização única.](#single-sign-out)

> [!NOTE]
> Utilizando [políticas personalizadas,](custom-policy-overview.md)pode desativar a sinalização dos fornecedores de identidade federados, definindo os metadados de perfil técnico do fornecedor de identidade `SingleLogoutEnabled` para `false` .

A sessão de sinalização apaga o estado de inscrição do utilizador com o Azure AD B2C, mas pode não assinar o utilizador fora da sua sessão de provedor de identidade social. Se o utilizador selecionar o mesmo fornecedor de identidade durante uma sintrodução subsequente, poderá reautentar sem introduzir as suas credenciais. Se um utilizador quiser assinar a aplicação, não significa necessariamente que queira assinar fora da sua conta do Facebook. No entanto, se forem utilizadas contas locais, a sessão do utilizador termina corretamente.

### <a name="single-sign-out"></a>Fim de sessão único 


> [!NOTE]
> Esta funcionalidade está limitada a [políticas personalizadas.](custom-policy-overview.md)

Quando redireciona o utilizador para o ponto final de assinatura Azure AD B2C (tanto para os protocolos OAuth2 como PARA SAML), o Azure AD B2C limpa a sessão do utilizador do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem o Azure AD B2C para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, o Azure AD B2C envia um pedido HTTP GET ao registo `LogoutUrl` de todas as aplicações a que o utilizador está atualmente inscrito.


As aplicações devem responder a este pedido, limpando qualquer sessão que identifique o utilizador e devolvendo uma `200` resposta. Se pretender apoiar uma única sinseção na sua aplicação, tem de implementar uma `LogoutUrl` no código da sua aplicação. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como [configurar o comportamento da sessão no fluxo do utilizador](session-behavior.md).
- Saiba como configurar o comportamento da [sessão em políticas personalizadas.](session-behavior-custom-policy.md)
