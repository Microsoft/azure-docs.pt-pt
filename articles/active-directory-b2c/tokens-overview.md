---
title: Visão geral dos tokens - Azure Ative Directory B2C
description: Conheça as fichas utilizadas no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186493"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Visão geral dos tokens no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Diretório Ativo Azure B2C (Azure AD B2C) emite vários tipos de fichas de segurança à medida que processa cada fluxo de [autenticação](application-types.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de ficha.

## <a name="token-types"></a>Tipos de fichas

O Azure AD B2C suporta os [protocolos OAuth 2.0 e OpenID Connect,](protocols-overview.md)que utilizam fichas para autenticação e acesso seguro aos recursos. Todas as fichas utilizadas no Azure AD B2C são [tokens web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm afirmações de informações sobre o portador e o sujeito do símbolo.

As seguintes fichas são utilizadas na comunicação com o Azure AD B2C:

- *Ficha de identificação* - Um JWT que contém alegações que pode usar para identificar utilizadores na sua aplicação. Esta ficha é enviada de forma segura em pedidos de comunicação http entre dois componentes da mesma aplicação ou serviço. Pode usar as reclamações num símbolo de identificação como entender. São geralmente utilizados para exibir informações sobre contas ou para tomar decisões de controlo de acesso numa aplicação. As fichas de identificação estão assinadas, mas não estão encriptadas. Quando a sua aplicação ou API receber um símbolo de identificação, deve validar a assinatura para provar que o símbolo é autêntico. A sua aplicação ou API também deve validar algumas reclamações no token para provar que é válida. Dependendo dos requisitos do cenário, as reclamações validadas por uma aplicação podem variar, mas a sua aplicação deve realizar algumas validações comuns de reclamações em todos os cenários.
- *Ficha de acesso* - Um JWT que contém alegações que pode usar para identificar as permissões concedidas às suas APIs. As fichas de acesso estão assinadas, mas não estão encriptadas. Os tokens de acesso são usados para fornecer acesso a APIs e servidores de recursos.  Quando a sua API recebe um sinal de acesso, deve validar a assinatura para provar que o símbolo é autêntico. A sua API também deve validar algumas reclamações no símbolo para provar que é válida. Dependendo dos requisitos do cenário, as reclamações validadas por uma aplicação podem variar, mas a sua aplicação deve realizar algumas validações comuns de reclamações em todos os cenários.
- *Token refresh* - Tokens refresh são usados para adquirir novos tokens de ID e tokens de acesso em um fluxo OAuth 2.0. Eles fornecem à sua aplicação acesso a longo prazo aos recursos em nome dos utilizadores sem necessidade de interação com esses utilizadores. As fichas de atualização são opacas à sua aplicação. São emitidas pelo Azure AD B2C e só podem ser inspecionadas e interpretadas pelo Azure AD B2C. São de longa duração, mas a sua candidatura não deve ser escrita com a expectativa de que um token refrescante dure um período de tempo específico. As fichas de atualização podem ser invalidadas a qualquer momento por uma variedade de razões. A única maneira de a sua aplicação saber se um token de atualização é válido é tentar resgatá-lo fazendo um pedido simbólico ao Azure AD B2C. Quando resgata um token refrescante para um novo símbolo, recebe um novo token refrescante na resposta simbólica. Guarde o novo símbolo de atualização. Substitui o token de atualização que usou anteriormente no pedido. Esta ação ajuda a garantir que as suas fichas de atualização permanecem válidas o máximo de tempo possível.

## <a name="endpoints"></a>Pontos Finais

Uma [aplicação registada](tutorial-register-applications.md) recebe tokens e comunica com o Azure AD B2C enviando pedidos para estes pontos finais:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Os símbolos de segurança que a sua aplicação recebe do `/authorize` `/token` Azure AD B2C podem vir dos pontos finais ou finais. Quando os tokens de `/authorize` ID são adquiridos a partir do ponto final, é feito usando o [fluxo implícito](implicit-flow-single-page-application.md), que é frequentemente usado para utilizadores que satisfazem em aplicações web baseadas em JavaScript. Quando os tokens de `/token` ID são adquiridos a partir do ponto final, é feito usando o fluxo de código de [autorização](openid-connect.md#get-a-token), que mantém o token escondido do navegador.

## <a name="claims"></a>Afirmações

Quando utilizar o Azure AD B2C, tem um controlo fino sobre o conteúdo dos seus tokens. Pode configurar [os fluxos](user-flow-overview.md) de utilizadores e [as políticas personalizadas](custom-policy-overview.md) para enviar certos conjuntos de dados do utilizador em sinistros que são necessários para a sua aplicação. Estas reclamações podem incluir propriedades padrão, tais como **displayName** e **emailAddress**. As suas aplicações podem utilizar estas reclamações para autenticar de forma segura utilizadores e pedidos.

As reclamações em fichas de identificação não são devolvidas em nenhuma ordem específica. Novas reclamações podem ser introduzidas em fichas de identificação a qualquer momento. A sua candidatura não deve quebrar à medida que novas reclamações são introduzidas. Também pode incluir [atributos de utilizador personalizados](user-flow-custom-attributes.md) nas suas reclamações.

A tabela que se segue lista as alegações que pode esperar em fichas de identificação e fichas de acesso emitidas pelo Azure AD B2C.

| Nome | Afirmação | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Audiência | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do símbolo. Para o Azure AD B2C, o público é o ID da aplicação. A sua aplicação deve validar este valor e rejeitar o símbolo se não corresponder. O público é sinónimo de recurso. |
| Emissor | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o serviço de fichas de segurança (STS) que constrói e devolve o símbolo. Identifica também o diretório em que o utilizador foi autenticado. O seu pedido deve validar a alegação do emitente para se certificar de que o símbolo veio do ponto final apropriado. |
| Emitido em | `iat` | `1438535543` | O tempo em que o símbolo foi emitido, representado na época. |
| Tempo de validade | `exp` | `1438539443` | O tempo em que o símbolo se torna inválido, representado na época. O seu pedido deve utilizar esta alegação para verificar a validade da vida útil do símbolo. |
| Não antes. | `nbf` | `1438535543` | O tempo em que o símbolo se torna válido, representado no tempo da época. Desta vez é geralmente a mesma que a hora em que o símbolo foi emitido. O seu pedido deve utilizar esta alegação para verificar a validade da vida útil do símbolo. |
| Versão | `ver` | `1.0` | A versão do símbolo id, tal como definida pelo Azure AD B2C. |
| Código hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código incluído num token de identificação apenas quando o símbolo é emitido juntamente com um código de autorização OAuth 2.0. Um hash código pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como realizar esta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de acesso incluído num token de identificação apenas quando o token é emitido juntamente com um token de acesso OAuth 2.0. Um hash de acesso pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como realizar esta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia usada para mitigar ataques de repetição de tokens. O seu pedido pode especificar um nonce `nonce` num pedido de autorização utilizando o parâmetro de consulta. O valor que fornece no pedido é emitido `nonce` não modificado apenas na reclamação de um token de identificação. Esta reclamação permite que a sua aplicação verifique o valor em relação ao valor especificado no pedido. A sua aplicação deverá efetuar esta validação durante o processo de validação do token ID. |
| Assunto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | O principal sobre o qual o símbolo afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser utilizado para efetuar controlos de autorização com segurança, como quando o símbolo é usado para aceder a um recurso. Por predefinição, a reclamação do sujeito é povoada com o ID do objeto do utilizador no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Usado apenas com políticas mais antigas. |
| Política-quadro de confiança | `tfp` | `b2c_1_signupsignin1` | O nome da apólice que foi usada para adquirir o símbolo de identificação. |
| Tempo de autenticação | `auth_time` | `1438535543` | O momento em que um utilizador entrou pela última vez credenciais, representadas na época. Não existe discriminação entre essa autenticação ser um novo login, uma única sessão de inscrição (SSO) ou outro tipo de inscrição. Esta `auth_time` é a última vez que a aplicação (ou utilizador) iniciou uma tentativa de autenticação contra o Azure AD B2C. O método utilizado para autenticar não é diferenciado. |
| Âmbito | `scp` | `Read`| As permissões concedidas ao recurso para um sinal de acesso. Múltiplas permissões concedidas são separadas por um espaço. |
| Parte Autorizada | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A identificação da **aplicação** do cliente que iniciou o pedido. |

## <a name="configuration"></a>Configuração

As seguintes propriedades são utilizadas para [gerir tokens de segurança emitidos](configure-tokens.md) pelo Azure AD B2C:

- Acesso & vida útil do **token (minutos)** - A vida útil do token oportador da OAuth 2.0 usado para ter acesso a um recurso protegido. O padrão é de 60 minutos. O mínimo (inclusivo) é de 5 minutos. O máximo (inclusivo) é de 1440 minutos.

- Refrescar a vida útil do **token (dias)** - O período máximo de tempo antes do qual um token de atualização pode ser usado para adquirir um novo acesso ou ficha de identificação. O período de tempo também abrange a aquisição de um `offline_access` novo token de atualização se a sua aplicação tiver sido concedida o âmbito. O padrão é de 14 dias. O mínimo (inclusivo) é um dia. O máximo (inclusivo) é de 90 dias.

- Refrescar a **janela deslizante de token (dias)** - Após este período de tempo, o utilizador é obrigado a reautenticar, independentemente do período de validade do mais recente token de atualização adquirido pela aplicação. Só pode ser fornecido se o interruptor estiver ligado a **Limitado**. Tem de ser maior ou igual ao valor de **vida token (dias)** do Refresh. Se o interruptor estiver definido para **Unbounded,** não pode fornecer um valor específico. O padrão é de 90 dias. O mínimo (inclusivo) é um dia. O máximo (inclusivo) é de 365 dias.

Os seguintes casos de utilização são ativados utilizando estas propriedades:

- Permita que um utilizador permaneça inscrito numa aplicação móvel indefinidamente, desde que o utilizador esteja continuamente ativo na aplicação. Pode definir a vida útil da **janela deslizante do token Refresh (dias)** para **desalimitada** no fluxo de utilizador de entrada.
- Cumpra os requisitos de segurança e conformidade da sua indústria, estabelecendo as vidas de acesso adequados.

Estas definições não estão disponíveis para redefinir os fluxos de utilizador da palavra-passe.

## <a name="compatibility"></a>Compatibilidade

As seguintes propriedades são utilizadas para gerir a [compatibilidade simbólica:](configure-tokens.md)

- **Emitente (iss) reivindicação** - Este imóvel identifica o inquilino Azure AD B2C que emitiu o símbolo. O valor predefinido é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` dos IDs para o inquilino Azure AD AD B2C e o fluxo de utilizador que foi utilizado no pedido simbólico. Se a sua aplicação ou biblioteca necessitar de Azure AD B2C para estar em conformidade com a [especificação OpenID Connect Discovery 1.0,](https://openid.net/specs/openid-connect-discovery-1_0.html)utilize este valor.

- **Reclamação (sub)** - Este imóvel identifica a entidade para a qual o símbolo afirma informações. O valor predefinido é **objectID**, que povoa a `sub` reclamação no símbolo com o ID do objeto do utilizador. O valor de **Não suportado** só é previsto para retrocompatibilidade. Recomenda-se que mude para **objectID** assim que puder.

- **Reivindicação que representa** id política - Esta propriedade identifica o tipo de reclamação no qual o nome de apólice utilizado no pedido simbólico é povoado. O valor predefinido é `tfp`. O valor `acr` só é previsto para retrocompatibilidade.

## <a name="pass-through"></a>Pass-through

Quando uma viagem de utilizador começa, o Azure AD B2C recebe um sinal de acesso de um fornecedor de identidade. O Azure AD B2C utiliza esse símbolo para recuperar informações sobre o utilizador. [Permite uma reclamação no fluxo do utilizador](idp-pass-through-user-flow.md) ou define uma [reclamação na sua política personalizada](idp-pass-through-custom.md) para passar o símbolo para as aplicações que regista no Azure AD B2C. A sua aplicação deve estar a utilizar um fluxo de [utilizador v2](user-flow-versions.md) para aproveitar a passagem do token como reclamação.

Atualmente, o Azure AD B2C apenas suporta a passagem do sinal de acesso dos fornecedores de identidade OAuth 2.0, que incluem o Facebook e a Google. Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

## <a name="validation"></a>Validação

Para validar um símbolo, a sua aplicação deverá verificar tanto a assinatura como as alegações do símbolo. Muitas bibliotecas de código aberto estão disponíveis para validação de JWTs, dependendo da sua língua preferida. Recomenda-se que explore essas opções em vez de implementar a sua própria lógica de validação.

### <a name="validate-signature"></a>Validar a assinatura

Um JWT contém três segmentos, um *cabeçalho,* um *corpo,* e uma *assinatura.* O segmento de assinatura pode ser usado para validar a autenticidade do símbolo para que possa ser confiado pela sua aplicação. Os tokens Azure AD B2C são assinados utilizando algoritmos de encriptação assimétrica padrão da indústria, como rSA 256.

O cabeçalho do token contém informações sobre o método chave e encriptação usado para assinar o símbolo:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor da reivindicação da **Alg** é o algoritmo que foi usado para assinar o símbolo. O valor da **alegação** infantil é a chave pública que foi usada para assinar o símbolo. A qualquer momento, o Azure AD B2C pode assinar um símbolo usando qualquer um de um conjunto de pares de chaves público-privados. Azure AD B2C gira periodicamente o possível conjunto de teclas. A sua aplicação deve ser escrita para lidar automaticamente com essas alterações de chave. Uma frequência razoável para verificar se há atualizações das teclas públicas utilizadas pelo Azure AD B2C é a cada 24 horas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Utilizando este ponto final, as aplicações podem solicitar informações sobre o Azure AD B2C em tempo de execução. Estas informações incluem pontos finais, conteúdo simbólico e chaves de assinatura simbólicas. O seu inquilino Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias peças de informação úteis. Os metadados contêm **jwks_uri,** que dá a localização do conjunto de chaves públicas que são usadas para assinar fichas. Essa localização é fornecida aqui, mas o melhor é obter a localização dinamicamente usando o documento de metadados e analisando **jwks_uri:**

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
O documento JSON localizado neste URL contém todas as informações de chave pública em uso num determinado momento. A sua aplicação pode utilizar a `kid` reclamação no cabeçalho JWT para selecionar a chave pública no documento JSON que é usado para assinar um símbolo específico. Pode então executar validação de assinatura utilizando a chave pública correta e o algoritmo indicado.

O documento de `B2C_1_signupsignin1` metadados `contoso.onmicrosoft.com` para a apólice no inquilino está localizado em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Para determinar que política foi usada para assinar um símbolo (e para onde ir para solicitar os metadados), tem duas opções. Em primeiro lugar, o nome `acr` da apólice está incluído na reclamação no símbolo. Pode analisar as alegações do corpo do JWT descodificando o corpo e desserializando a cadeia JSON que resulta. A `acr` alegação é o nome da política que foi usada para emitir o símbolo. A outra opção é codificar a `state` política no valor do parâmetro quando emitir o pedido e, em seguida, descodificá-la para determinar qual a política utilizada. Qualquer um dos métodos é válido.

Uma descrição de como realizar a validação de assinaturas está fora do âmbito deste documento. Muitas bibliotecas de código aberto estão disponíveis para ajudá-lo a validar um símbolo.

### <a name="validate-claims"></a>Validar reclamações

Quando as suas aplicações ou API recebem um token de identificação, também deve efetuar várias verificações contra as reclamações no token ID. Devem ser verificados os seguintes pedidos:

- **público** - Verifica que o símbolo de identificação foi destinado a ser dado à sua aplicação.
- **não antes** e **o tempo** de validade - Verifica que o token de identificação não expirou.
- **emitente** - Verifica que o token foi emitido para a sua aplicação pelo Azure AD B2C.
- **nonce** - Uma estratégia para a mitigação do ataque de repetição de token.

Para obter uma lista completa de validações que a sua aplicação deve executar, consulte a [especificação OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [usar fichas](access-tokens.md)de acesso.

