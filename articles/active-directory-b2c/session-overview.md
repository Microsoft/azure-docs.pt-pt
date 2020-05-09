---
title: Sessões SSO no Diretório Ativo Azure B2C [ Microsoft Docs
description: Configure o comportamento da sessão no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea8c40faad4ee709ae98f868e36fd42e46501bea
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927042"
---
# <a name="azure-ad-b2c-session"></a>Sessão Azure AD B2C

O único sinal (SSO) adiciona segurança e comodidade quando os utilizadores assinam através de aplicações no Azure Ative Directory B2C (Azure AD B2C). Este artigo descreve os métodos de inscrição únicos utilizados no Azure AD B2C e ajuda-o a escolher o método SSO mais adequado ao configurar a sua política.

Com um único registo, os utilizadores assinam uma vez com uma única conta e têm acesso a várias aplicações. A aplicação pode ser uma aplicação web, móvel ou de página única, independentemente da plataforma ou nome de domínio.

Quando o utilizador inicialmente inicia o insessão numa aplicação, o Azure AD B2C persiste numa sessão baseada em cookies. Após pedidos de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookies, e emite um sinal de acesso sem levar o utilizador a iniciar sessão novamente. Se a sessão baseada em cookies expirar ou se tornar inválida, o utilizador é solicitado a iniciar novamente o insessão.  

## <a name="sso-session-types"></a>Tipos de sessão SSO

A integração com o Azure AD B2C envolve três tipos de sessões SSO:

- **Azure AD B2C** - Sessão gerida por Azure AD B2C
- **Fornecedor de identidade federado** - Sessão gerida pelo fornecedor de identidade, por exemplo Facebook, Salesforce ou conta Microsoft
- **Aplicação** - Sessão gerida pela aplicação web, móvel ou página única

![Sessão de SSO](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

Quando um utilizador autentica com sucesso com uma conta local ou social, o Azure AD B2C armazena uma sessão baseada em cookies no navegador do utilizador. O cookie é armazenado sob o nome de domínio do `https://contoso.b2clogin.com`inquilino Azure AD B2C, como .

Se um utilizador inicialmente faz o insessão com uma conta federada, e depois durante a janela de tempo da sessão (hora de viver, ou TTL) entra na mesma app ou numa aplicação diferente, o Azure AD B2C tenta adquirir um novo sinal de acesso ao fornecedor de identidade federado. Se a sessão de fornecedor de identidade federada estiver caducada ou inválida, o fornecedor de identidade federado solicita ao utilizador as suas credenciais. Se a sessão ainda estiver ativa (ou se o utilizador tiver assinado com uma conta local em vez de uma conta federada), o Azure AD B2C autoriza o utilizador e elimina novas solicitações.

Pode configurar o comportamento da sessão, incluindo a sessão TTL e como o Azure AD B2C partilha a sessão através de políticas e aplicações.

### <a name="federated-identity-provider-sso"></a>Provedor de identidade federado SSO

Um fornecedor de identidade social ou empresarial gere a sua própria sessão. O cookie é armazenado sob o nome de `https://login.salesforce.com`domínio do fornecedor de identidade, como . O Azure AD B2C não controla a sessão de fornecedor de identidade federada. Em vez disso, o comportamento da sessão é determinado pelo fornecedor de identidade federado. 

Considere o seguinte cenário:

1. Um utilizador assina no Facebook para verificar o seu feed.
2. Mais tarde, o utilizador abre a sua aplicação e inicia o processo de início de sessão. A aplicação redireciona o utilizador para o Azure AD B2C para completar o processo de início de sessão.
3. Na página de inscrição ou inscrição do Azure AD B2C, o utilizador opta por iniciar sessão com a sua conta de Facebook. O utilizador é redirecionado para o Facebook. Se houver uma sessão ativa no Facebook, o utilizador não é solicitado a fornecer as suas credenciais e é imediatamente redirecionado para O Azure AD B2C com um token do Facebook.

### <a name="application-sso"></a>Aplicação SSO

Uma aplicação web, móvel ou de uma única página pode ser protegida por acesso suth oAuth, fichas de identificação ou fichas SAML. Quando um utilizador tenta aceder a um recurso protegido na aplicação, a aplicação verifica se existe uma sessão ativa no lado da aplicação. Se não houver nenhuma sessão de aplicações ou a sessão tiver expirado, a aplicação levará o utilizador ao Azure AD B2C para assinar a página.

A sessão de candidatura pode ser uma sessão baseada `https://contoso.com`em cookies armazenada sob o nome de domínio da aplicação, como . As aplicações móveis podem armazenar a sessão de uma forma diferente, mas usando uma abordagem semelhante.

## <a name="azure-ad-b2c-session-configuration"></a>Configuração da sessão Azure AD B2C

### <a name="session-scope"></a>Âmbito da sessão

A sessão Azure AD B2C pode ser configurada com os seguintes âmbitos:

- **Inquilino** - Esta definição é o padrão. A utilização desta definição permite que várias aplicações e fluxos de utilizadores no seu inquilino B2C partilhem a mesma sessão de utilizador. Por exemplo, uma vez que um utilizador assina numa aplicação, o utilizador também pode iniciar sem problemas outra ao aceder à sua.
- **Aplicação** - Esta definição permite manter uma sessão de utilizador exclusivamente para uma aplicação, independente de outras aplicações. Por exemplo, pode utilizar esta definição se quiser que o utilizador assine a Farmácia Contoso, independentemente de o utilizador já estar inscrito nas Mercearias Contoso.
- **Política** - Esta definição permite manter uma sessão de utilizador exclusivamente para um fluxo de utilizador, independentemente das aplicações que a utilizam. Por exemplo, se o utilizador já tiver assinado e concluído uma etapa de autenticação multifactor (MFA), o utilizador pode ter acesso a partes de maior segurança de várias aplicações, desde que a sessão ligada ao fluxo do utilizador não expire.
- **Desativado** - Esta definição obriga o utilizador a executar todo o fluxo do utilizador em todas as execuções da apólice.

### <a name="session-life-time"></a>Tempo de vida da sessão

O tempo de vida da **sessão** é o tempo que o cookie de sessão Azure AD B2C é armazenado no navegador do utilizador após a autenticação bem sucedida. Pode definir o tempo de vida da sessão para um valor de 15 a 720 minutos.

### <a name="keep-me-signed-in"></a>Manter a sessão iniciada

A funcionalidade [Keep me signed-in](custom-policy-keep-me-signed-in.md) prolonga o tempo de vida da sessão através do uso de um cookie persistente. A sessão permanece ativa após o utilizador fechar e reabrir o navegador. A sessão só é revogada quando um utilizador assina. A funcionalidade "Manter-me inscrito" só se aplica ao sessão com as contas locais.

A funcionalidade "Keep me signed-in" tem precedência sobre o tempo de vida da sessão. Se a funcionalidade Keep me signed-in estiver ativada e o utilizador a selecionar, esta funcionalidade dita quando a sessão expirar. 

### <a name="session-expiry-type"></a>Tipo de expiração da sessão

O tipo de expiração da **sessão** indica como uma sessão é prolongada pela definição de tempo de vida da sessão ou pela definição de me sessão.

- **Rolling** - Indica que a sessão é prolongada sempre que o utilizador executa uma autenticação baseada em cookies (predefinição).
- **Absoluto** - Indica que o utilizador é obrigado a reautenticar após o período de tempo especificado.

## <a name="sign-out"></a>Terminar sessão

Quando pretende assinar o utilizador fora da aplicação, não basta limpar os cookies da aplicação ou terminar a sessão com o utilizador. Tem de redirecionar o utilizador para o Azure AD B2C para assinar. Caso contrário, o utilizador poderá reautenticar as suas aplicações sem voltar a introduzir as suas credenciais.

Mediante um pedido de inscrição, Azure AD B2C:

1. Invalida a sessão baseada em cookies Azure AD B2C.
1. Tentativas de assinatura de fornecedores de identidade federados:
   - OpenId Connect - Se o ponto final de `end_session_endpoint` configuração bem conhecido do fornecedor de identidade especificar uma localização.
   - SAML - Se os metadados `SingleLogoutService` do fornecedor de identidade contiverem a localização.
1. Opcionalmente, despede-se de outras aplicações. Para mais informações, consulte a secção [de inscrição single.](#single-sign-out)

O sign-out iliba o único estado de inscrição do utilizador com o Azure AD B2C, mas pode não assinar o utilizador fora da sua sessão de fornecedor de identidade social. Se o utilizador selecionar o mesmo fornecedor de identidade durante um início de sessão subsequente, poderá reautenticar sem introduzir as suas credenciais. Se um utilizador quiser assinar a aplicação, isso não significa necessariamente que queira assinar a sua conta de Facebook. No entanto, se as contas locais forem utilizadas, a sessão do utilizador termina corretamente.

### <a name="single-sign-out"></a>Fim de sessão único 


> [!NOTE]
> Esta funcionalidade limita-se a [políticas personalizadas.](custom-policy-overview.md)

Ao redirecionar o utilizador para o ponto final de sinalização Azure AD B2C (para os protocolos OAuth2 e SAML), o Azure AD B2C iliba a sessão do utilizador a partir do navegador. No entanto, o utilizador poderá ainda ser inscrito noutras aplicações que utilizem o Azure AD B2C para autenticação. Para permitir que essas aplicações assinem o utilizador em simultâneo, o `LogoutUrl` Azure AD B2C envia um pedido HTTP GET ao registo de todas as aplicações a que o utilizador está atualmente inscrito.


As aplicações devem responder a este pedido, limpando `200` qualquer sessão que identifique o utilizador e devolvendo uma resposta. Se pretender apoiar uma única inscrição na sua `LogoutUrl` aplicação, tem de implementar um no código da sua aplicação. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o comportamento da [sessão no fluxo do utilizador](session-behavior.md).
- Saiba como configurar o comportamento da [sessão em políticas personalizadas.](session-behavior-custom-policy.md)