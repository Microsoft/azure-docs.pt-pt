---
title: Visão geral dos tokens - Azure Ative Directory B2C
description: Saiba mais sobre os tokens usados no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 19b65554801a22954499219e43ed021a7cc8c121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89258440"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Visão geral dos tokens no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) emite vários tipos de fichas de segurança à medida que processa cada [fluxo de autenticação](application-types.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## <a name="token-types"></a>Tipos de token

O Azure AD B2C suporta os [protocolos OAuth 2.0 e OpenID Connect,](protocols-overview.md)que utiliza tokens para autenticação e acesso seguro aos recursos. Todos os tokens utilizados no Azure AD B2C são [fichas web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm afirmações de informações sobre o portador e o tema do token.

Os seguintes tokens são utilizados em comunicação com a Azure AD B2C:

- *ID token* - Um JWT que contém alegações que pode usar para identificar utilizadores na sua aplicação. Este token é enviado de forma segura em pedidos HTTP para comunicação entre dois componentes da mesma aplicação ou serviço. Pode usar as reclamações num token de identificação como quiser. São normalmente utilizados para exibir informações sobre contas ou para tomar decisões de controlo de acesso numa aplicação. As fichas de identificação estão assinadas, mas não são encriptadas. Quando a sua aplicação ou API recebe um token de ID, deve validar a assinatura para provar que o token é autêntico. A sua aplicação ou API também deve validar algumas reclamações no token para provar que é válida. Dependendo dos requisitos do cenário, as reclamações validadas por uma aplicação podem variar, mas a sua aplicação deve realizar algumas validações de reclamações comuns em todos os cenários.
- *Token de acesso* - Um JWT que contém alegações que pode usar para identificar as permissões concedidas às suas APIs. Os tokens de acesso estão assinados, mas não são encriptados. Os tokens de acesso são usados para fornecer acesso a APIs e servidores de recursos.  Quando a sua API recebe um token de acesso, deve validar a assinatura para provar que o token é autêntico. A sua API também deve validar algumas reclamações no token para provar que é válida. Dependendo dos requisitos do cenário, as reclamações validadas por uma aplicação podem variar, mas a sua aplicação deve realizar algumas validações de reclamações comuns em todos os cenários.
- *Refresh tokens* - Refresh tokens são usados para adquirir novos tokens de ID e tokens de acesso num fluxo OAuth 2.0. Fornecem à sua aplicação acesso a recursos a longo prazo em nome dos utilizadores sem que seja necessário interação com esses utilizadores. As fichas de atualização são opacas à sua aplicação. São emitidas pela Azure AD B2C e só podem ser inspecionadas e interpretadas pela Azure AD B2C. São de longa duração, mas a sua candidatura não deve ser escrita com a expectativa de que um token de atualização dure um determinado período de tempo. As fichas de atualização podem ser invalidadas a qualquer momento por uma variedade de razões. A única maneira de a sua aplicação saber se um token de atualização é válido é tentar resgatá-lo fazendo um pedido simbólico ao Azure AD B2C. Quando você resgatar um token de atualização para um novo token, você recebe um novo token de atualização na resposta simbólica. Guarde o novo token de atualização. Substitui o token de atualização que usou anteriormente no pedido. Esta ação ajuda a garantir que as suas fichas de atualização permaneçam válidas durante o maior tempo possível.

## <a name="endpoints"></a>Pontos Finais

Uma [aplicação registada](tutorial-register-applications.md) recebe fichas e comunica com a Azure AD B2C enviando pedidos para estes pontos finais:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Os tokens de segurança que a sua aplicação recebe do Azure AD B2C podem vir dos `/authorize` `/token` pontos finais ou finais. Quando os tokens de ID são adquiridos a partir do `/authorize` ponto final, é feito usando o [fluxo implícito](implicit-flow-single-page-application.md), que é frequentemente usado para utilizadores que inscrevem em aplicações web baseadas em JavaScript. Quando os tokens de ID são adquiridos a partir do `/token` ponto final, é feito usando o [fluxo](openid-connect.md#get-a-token)de código de autorização , que mantém o token escondido do navegador.

## <a name="claims"></a>Afirmações

Quando utilizar o Azure AD B2C, tem um controlo fino sobre o conteúdo das suas fichas. Pode configurar fluxos de utilizador e [políticas personalizadas](custom-policy-overview.md) para enviar [determinados conjuntos](user-flow-overview.md) de dados do utilizador em reclamações que são necessárias para a sua aplicação. Estas reclamações podem incluir propriedades padrão, tais como **displayName** e **emailAddress**. As suas aplicações podem utilizar estas reclamações para autenticar de forma segura os utilizadores e pedidos.

As reclamações em fichas de identificação não são devolvidas em nenhuma ordem particular. Novas reclamações podem ser introduzidas em fichas de identificação a qualquer momento. A sua aplicação não deve quebrar à medida que novas reclamações são introduzidas. Também pode incluir [atributos personalizados do utilizador](user-flow-custom-attributes.md) nas suas reclamações.

A tabela que se segue lista as alegações que pode esperar em fichas de identificação e fichas de acesso emitidas pela Azure AD B2C.

| Nome | Afirmação | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Audiência | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do token. Para Azure AD B2C, o público é o ID da aplicação. A sua aplicação deve validar este valor e rejeitar o token se não corresponder. O público é sinónimo de recurso. |
| Emissor | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o serviço de símbolos de segurança (STS) que constrói e devolve o token. Identifica também o diretório em que o utilizador foi autenticado. A sua aplicação deverá validar a alegação do emitente para garantir que o token provém do ponto final apropriado. |
| Emitida em | `iat` | `1438535543` | O momento em que o símbolo foi emitido, representado no tempo da época. |
| Tempo de validade | `exp` | `1438539443` | O momento em que o símbolo se torna inválido, representado no tempo da época. A sua aplicação deve utilizar esta alegação para verificar a validade da vida útil do token. |
| Não antes | `nbf` | `1438535543` | O momento em que o símbolo se torna válido, representado no tempo da época. Desta vez é geralmente o mesmo que a hora em que o símbolo foi emitido. A sua aplicação deve utilizar esta alegação para verificar a validade da vida útil do token. |
| Versão | `ver` | `1.0` | A versão do token ID, tal como definido por Azure AD B2C. |
| Haxixe de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um haxixe de código incluído num símbolo de identificação apenas quando o token é emitido juntamente com um código de autorização OAuth 2.0. Um haxixe de código pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como realizar esta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Aceda a haxixe simbólico | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de acesso incluído num símbolo de identificação apenas quando o token é emitido juntamente com um token de acesso OAuth 2.0. Um hash de acesso pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como realizar esta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia usada para mitigar ataques de repetição simbólica. O seu pedido pode especificar um nó num pedido de autorização utilizando o `nonce` parâmetro de consulta. O valor que fornece no pedido é emitido sem modificação na `nonce` reivindicação de um símbolo de identificação apenas. Esta reclamação permite que o seu pedido verifique o valor contra o valor especificado no pedido. A sua aplicação deverá efetuar esta validação durante o processo de validação do iDken. |
| Assunto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | O principal sobre o qual o símbolo afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser usado para efetuar verificações de autorização com segurança, como quando o token é usado para aceder a um recurso. Por predefinição, a reclamação do sujeito é povoada com o ID do objeto do utilizador no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Usado apenas com políticas mais antigas. |
| Política de enquadramento de confiança | `tfp` | `b2c_1_signupsignin1` | O nome da apólice que foi usada para adquirir o símbolo de identificação. |
| Tempo de autenticação | `auth_time` | `1438535543` | O momento em que um utilizador inseriu credenciais pela última vez, representadas em tempo de época. Não há discriminação entre essa autenticação ser um novo sinal, uma sessão de sessão de sessão de sso ou outro tipo de inscrição. Esta `auth_time` é a última vez que a aplicação (ou utilizador) iniciou uma tentativa de autenticação contra o Azure AD B2C. O método utilizado para autenticar não é diferenciado. |
| Âmbito | `scp` | `Read`| As permissões concedidas ao recurso para um token de acesso. Várias permissões concedidas são separadas por um espaço. |
| Parte Autorizada | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A **identificação** do pedido de cliente que iniciou o pedido. |

## <a name="configuration"></a>Configuração

As seguintes propriedades são utilizadas para [gerir as vidas de fichas de segurança emitidas](configure-tokens.md) pela Azure AD B2C:

- **Acesso & id token lifes (minutos)** - A vida útil do símbolo portador de OAuth 2.0 usado para ter acesso a um recurso protegido. O padrão é de 60 minutos. O mínimo (inclusivo) é de 5 minutos. O máximo (inclusive) é de 1440 minutos.

- **Atualizar a vida útil (dias)** - O período máximo de tempo antes do qual um token de atualização pode ser usado para adquirir um novo acesso ou ficha de ID. O período de tempo também abrange a aquisição de um novo token de atualização se o seu pedido tiver sido concedido o `offline_access` âmbito. O padrão é de 14 dias. O mínimo (inclusivo) é um dia. O máximo (inclusive) é de 90 dias.

- **Atualizar a vida útil da janela de deslizamento (dias)** - Após este período de tempo, o utilizador é obrigado a reauttenticar, independentemente do período de validade do mais recente token de atualização adquirido pela aplicação. Só pode ser fornecido se o interruptor estiver definido para **"Bounded".** Tem de ser maior ou igual ao valor de **vida (dias) da Refresh Token.** Se o interruptor estiver definido para **Unbounded,** não é possível fornecer um valor específico. O padrão é de 90 dias. O mínimo (inclusivo) é um dia. O máximo (inclusive) é de 365 dias.

Os seguintes casos de utilização são habilitados a utilizar estas propriedades:

- Permitir que um utilizador permaneça inscrito numa aplicação móvel indefinidamente, desde que o utilizador esteja continuamente ativo na aplicação. Pode **configurar a vida útil da janela de deslizamento de fichas refresh (dias)** para **Desvinculada** no fluxo do utilizador de entrada.
- Satisfaça os requisitos de segurança e conformidade da sua indústria definindo as vidas de acesso adequadas.

Estas definições não estão disponíveis para repor os fluxos do utilizador.

## <a name="compatibility"></a>Compatibilidade

As seguintes propriedades são utilizadas [para gerir a compatibilidade simbólica:](configure-tokens.md)

- **Emitente (iss) reivindicação** - Este imóvel identifica o inquilino Azure AD B2C que emitiu o símbolo. O valor predefinido é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor inclui `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` iDs tanto para o inquilino Azure AD B2C como para o fluxo de utilizador que foi usado no pedido simbólico. Se a sua aplicação ou biblioteca necessitar do Azure AD B2C para estar em conformidade com a [especificação OpenID Connect Discovery 1.0,](https://openid.net/specs/openid-connect-discovery-1_0.html)utilize este valor.

- **Reclamação de sujeito (sub)** - Esta propriedade identifica a entidade para a qual o símbolo afirma informações. O valor predefinido é **ObjectID,** que povoa a `sub` reclamação no token com o ID do objeto do utilizador. O valor de Não suportado é **apenas** previsto para retrocompatibilidade. Recomenda-se que mude para **ObjectID** assim que puder.

- **Reclamação que representa o ID da política** - Esta propriedade identifica o tipo de reclamação em que o nome da apólice utilizado no pedido simbólico é povoado. O valor predefinido é `tfp`. O valor `acr` de só está previsto para retrocompatibilidade.

## <a name="pass-through"></a>Pass-through

Quando uma viagem de utilizador começa, a Azure AD B2C recebe um token de acesso de um fornecedor de identidade. O Azure AD B2C utiliza esse símbolo para obter informações sobre o utilizador. [Você ativa uma reclamação no fluxo do seu utilizador](idp-pass-through-user-flow.md) ou define uma [reclamação na sua política personalizada](idp-pass-through-custom.md) para passar o token através das aplicações que regista no Azure AD B2C. A sua aplicação deve estar a utilizar um [fluxo de utilizador recomendado](user-flow-versions.md) para aproveitar a passagem do token como reivindicação.

Atualmente, o Azure AD B2C apenas suporta a passagem do token de acesso de fornecedores de identidade OAuth 2.0, que incluem o Facebook e o Google. Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

## <a name="validation"></a>Validação

Para validar um token, o seu pedido deve verificar tanto a assinatura como as reclamações do token. Muitas bibliotecas de código aberto estão disponíveis para validar JWTs, dependendo do seu idioma preferido. Recomenda-se que explore essas opções em vez de implementar a sua própria lógica de validação.

### <a name="validate-signature"></a>Validar assinatura

Um JWT contém três segmentos, um *cabeçalho,* um *corpo,* e uma *assinatura*. O segmento de assinatura pode ser usado para validar a autenticidade do token para que possa ser confiável pela sua aplicação. Os tokens AD B2C do Azure são assinados utilizando algoritmos de encriptação assimétrica padrão da indústria, tais como RSA 256.

O cabeçalho do token contém informações sobre a chave e o método de encriptação usado para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor da alegação **da ALG** é o algoritmo que foi usado para assinar o token. O valor da alegação do **miúdo** é a chave pública que foi usada para assinar o símbolo. A qualquer momento, a Azure AD B2C pode assinar um token usando qualquer um de um conjunto de pares de chaves público-privado. Azure AD B2C roda periodicamente o possível conjunto de teclas. A sua aplicação deve ser escrita para lidar automaticamente com essas alterações de teclas. Uma frequência razoável para verificar se há atualizações nas chaves públicas utilizadas pelo Azure AD B2C a cada 24 horas. Para lidar com alterações inesperadas das chaves, a sua aplicação deve ser escrita para recuperar as chaves públicas se receber um valor **inesperado para crianças.**

Azure AD B2C tem um ponto final de ligação de metadados OpenID. Utilizando este ponto final, as aplicações podem solicitar informações sobre o Azure AD B2C no tempo de execução. Esta informação inclui pontos finais, conteúdo simbólico e chaves de assinatura simbólicas. O seu inquilino Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias informações úteis. Os metadados contêm **jwks_uri,** que dá a localização do conjunto de chaves públicas que são usadas para assinar fichas. Essa localização é fornecida aqui, mas o melhor é obter a localização dinamicamente usando o documento de metadados e analisando **jwks_uri:**

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
O documento JSON localizado neste URL contém todas as informações chave do público em uso num determinado momento. A sua aplicação pode utilizar a `kid` reclamação no cabeçalho JWT para selecionar a chave pública no documento JSON que é usado para assinar um sinal particular. Em seguida, pode efetuar validação de assinatura usando a chave pública correta e o algoritmo indicado.

O documento de metadados para a `B2C_1_signupsignin1` apólice no `contoso.onmicrosoft.com` arrendatário situa-se em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Para determinar que política foi usada para assinar um token (e para onde ir para solicitar os metadados), tem duas opções. Em primeiro lugar, o nome da apólice está incluído `acr` na reivindicação no token. Pode analisar as reclamações do corpo do JWT descodindo o corpo e deserizando a cadeia JSON que resulta. A `acr` alegação é o nome da política que foi usada para emitir o símbolo. A outra opção é codificar a política no valor do `state` parâmetro quando emite o pedido e, em seguida, descodificá-la para determinar que política foi utilizada. Qualquer um dos métodos é válido.

Uma descrição de como realizar validação de assinaturas está fora do âmbito deste documento. Muitas bibliotecas de código aberto estão disponíveis para ajudá-lo a validar um token.

### <a name="validate-claims"></a>Validar reclamações

Quando as suas aplicações ou API recebem um token de ID, também deve efetuar vários controlos contra as reclamações no token de ID. As seguintes alegações devem ser verificadas:

- **público** - Verifica que o token de ID foi destinado a ser dado à sua aplicação.
- **não antes** e **prazo de validade** - Verifique se o token de identificação não expirou.
- **emitente** - Verifica se o token foi emitido para o seu pedido pela Azure AD B2C.
- **nonce** - Uma estratégia para a mitigação do ataque de repetição simbólica.

Para obter uma lista completa de validações que a sua aplicação deve realizar, consulte a [especificação OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [usar fichas de acesso.](access-tokens.md)

