---
title: Vidas simbólicas configuráveis
titleSuffix: Microsoft identity platform
description: Saiba como definir as vidas para acesso, fichas SAML e ID emitidas pela plataforma de identidade microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4ae26a489b823e2347841cf72690d6cd8462611
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755307"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Vidas de token configuráveis na plataforma de identidade da Microsoft (pré-visualização)

Pode especificar o tempo de vida útil de um token de acesso, ID ou SAML emitido pela plataforma de identidade Microsoft. Pode definir durações de tokens para todas as aplicações existentes na sua organização, para uma aplicação multi-inquilino (com várias organizações) ou para um principal de serviço específico na sua organização. No entanto, atualmente, não apoiamos a configuração das vidas simbólicas para [os diretores de serviços de identidade geridos.](../managed-identities-azure-resources/overview.md)

Em Azure AD, um objeto de política representa um conjunto de regras que são aplicadas em aplicações individuais ou em todas as aplicações de uma organização. Cada tipo de política tem uma estrutura única, com um conjunto de propriedades que são aplicadas a objetos aos quais são atribuídos.

Pode designar uma política como a política padrão para a sua organização. A política é aplicada a qualquer aplicação na organização, desde que não seja ultrapassada por uma política com maior prioridade. Também pode atribuir uma apólice a aplicações específicas. A ordem de prioridade varia consoam em comparação com o tipo de política.

Por exemplo, leia [exemplos de como configurar vidas simbólicas.](configure-token-lifetimes.md)

> [!NOTE]
> A política de vida de token configurada aplica-se apenas a clientes móveis e desktop que acedam ao SharePoint Online e ao OneDrive para recursos empresariais, e não se aplicam a sessões de navegador da Web.
> Para gerir o tempo de vida útil das sessões de navegador web para SharePoint Online e OneDrive para negócios, utilize a funcionalidade de vida útil da [sessão de acesso condicional.](../conditional-access/howto-conditional-access-session-lifetime.md) Consulte o [blog SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre configurar os intervalos de sessão ocioso.

## <a name="license-requirements"></a>Requisitos de licença

A utilização desta funcionalidade requer uma licença Azure AD Premium P1. Para encontrar a licença certa para os seus requisitos, consulte [Comparar funcionalidades geralmente disponíveis das edições Free and Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Os clientes com [licenças de negócios microsoft 365](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a funcionalidades de Acesso Condicional.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Token políticas vitalícias para acesso, SAML e fichas de ID

Você pode definir políticas de vida útil para fichas de acesso, fichas SAML e fichas de identificação. 

### <a name="access-tokens"></a>Tokens de acesso

Os clientes usam fichas de acesso para aceder a um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de utilizador, cliente e recurso. As fichas de acesso não podem ser revogadas e são válidas até ao seu termo. Um ator malicioso que obteve um símbolo de acesso pode usá-lo para a extensão da sua vida. Ajustar a vida útil de um token de acesso é uma compensação entre melhorar o desempenho do sistema e aumentar o tempo que o cliente mantém o acesso após a desativação da conta do utilizador. O melhor desempenho do sistema é alcançado reduzindo o número de vezes que um cliente precisa para adquirir um novo token de acesso.  O padrão é de 1 hora - após 1 hora, o cliente deve usar o token de atualização para (geralmente silenciosamente) adquirir um novo token de atualização e ficha de acesso.

### <a name="saml-tokens"></a>Tokens SAML

Os tokens SAML são usados por muitas aplicações SaaS baseadas na web, e são obtidos usando o ponto final do protocolo SAML2 do Azure Ative Directory. Também são consumidos por aplicações que utilizam a WS-Federation. A vida útil padrão do símbolo é de 1 hora. Do ponto de vista da aplicação, o período de validade do token é especificado pelo valor notOnOrAfter do `<conditions …>` elemento no token. Após o fim do período de validade do token, o cliente deve iniciar um novo pedido de autenticação, que muitas vezes será satisfeito sem o sinal interativo como resultado do token de Sessão de Sinal Único (SSO).

O valor de NotOnOrAfter pode ser alterado usando o `AccessTokenLifetime` parâmetro num `TokenLifetimePolicy` . Será definido para a vida útil configurada na apólice, se houver, mais um fator de distorção do relógio de cinco minutos.

A confirmação do assunto NotOnOrAfter especificada no `<SubjectConfirmationData>` elemento não é afetada pela configuração Token Lifetime. 

### <a name="id-tokens"></a>Tokens de ID

As fichas de identificação são passadas para websites e clientes nativos. As fichas de identificação contêm informações de perfil sobre um utilizador. Um símbolo de identificação está ligado a uma combinação específica de utilizador e cliente. As fichas de identificação são consideradas válidas até ao seu termo. Normalmente, uma aplicação web corresponde à vida útil de um utilizador na aplicação ao tempo de vida útil do token de ID emitido para o utilizador. Pode ajustar o tempo de vida útil de um token de ID para controlar a frequência com que a aplicação web expira na sessão de aplicação, e com que frequência requer que o utilizador seja reautencido com a plataforma de identidade da Microsoft (silenciosamente ou interativamente).

### <a name="token-lifetime-policy-properties"></a>Propriedades políticas de vida útil token

Uma política de vida simbólica é um tipo de objeto político que contém regras simbólicas de vida. Esta política controla quanto tempo o acesso, SAML e fichas de identificação para este recurso são considerados válidos. Se não for definida nenhuma política, o sistema aplica o valor de vida predefinido. 

A redução da propriedade Access Token Lifetime atenua o risco de um token de acesso ou símbolo de ID ser usado por um ator malicioso por um longo período de tempo. (Estas fichas não podem ser revogadas.) A compensação é que o desempenho é afetado negativamente, porque os tokens têm de ser substituídos com mais frequência.

Por exemplo, consulte [Criar uma política para iniciar sessão web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

| Propriedade | Cadeia de propriedade política | Afeta | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Access Token Lifetime |AccessTokenLifetime |Fichas de acesso, fichas de ID, fichas SAML2 |Uma hora |10 minutos |1 dia |

> [!NOTE]
> Para garantir que o cliente Web da Microsoft Teams funciona, recomenda-se manter o AccessTokenLifetime a mais de 15 minutos para as Equipas microsoft.

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Políticas de vida útil simbólicas para tokens de atualização e fichas de sessão

Você pode definir políticas de vida útil para tokens de atualização e fichas de sessão.

> [!IMPORTANT]
> A partir de maio de 2020, os novos inquilinos não podem configurar atualização e a sessão simbólica de vida.  Os inquilinos com configuração existente podem modificar as políticas de atualização e sessão até 30 de janeiro de 2021.   O Azure Ative Directory deixará de honrar a configuração de atualização e ficha de sessão existente em políticas após 30 de janeiro de 2021. Você ainda pode configurar o acesso, SAML, e id token vidas após a aposentadoria.
>
> Se precisar de continuar a definir o período de tempo antes de um utilizador ser convidado a iniciar novamente o súmis, configurar a frequência de inscrição no Acesso Condicional. Para saber mais sobre Acesso Condicional, leia a gestão da [sessão de autenticação configurada com Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)
>
> Se não pretender utilizar o Acesso Condicional após a data de reforma, as suas fichas de atualização e sessão serão definidas como [configuração predefinitiva](#configurable-token-lifetime-properties-after-the-retirement) nessa data e deixará de poder alterar as suas vidas.
>
> A vida útil do símbolo existente não será alterada. Após o seu termo, um novo token será emitido com base no valor padrão.

:::image type="content" source="./media/active-directory-configurable-token-lifetimes/roadmap.svg" alt-text="Informações sobre aposentadoria":::

### <a name="refresh-tokens"></a>Fichas de atualização

Quando um cliente adquire um token de acesso para aceder a um recurso protegido, o cliente também recebe um token de atualização. O token refresh é usado para obter novos pares de token de acesso/atualização quando o token de acesso atual expirar. Um token de atualização está ligado a uma combinação de utilizador e cliente. Um token de atualização pode ser [revogado a qualquer momento](access-tokens.md#token-revocation), e a validade do token é verificada sempre que o token é usado.  Os tokens refresh não são revogados quando usados para obter novos tokens de acesso - é melhor prática, no entanto, apagar de forma segura o velho token quando se consegue um novo.

É importante fazer uma distinção entre clientes confidenciais e clientes públicos, pois isso tem impacto no tempo de utilização de tokens de atualização. Para obter mais informações sobre diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Vidas simbólicas com fichas confidenciais de atualização de clientes
Os clientes confidenciais são aplicações que podem armazenar de forma segura uma palavra-passe do cliente (segredo). Podem provar que os pedidos vêm da aplicação de clientes seguros e não de um ator malicioso. Por exemplo, uma aplicação web é um cliente confidencial porque pode armazenar um segredo de cliente no servidor web. Não está exposto. Como estes fluxos são mais seguros, as vidas padrão dos tokens de atualização emitidos para estes fluxos `until-revoked` são , não podem ser alterados usando a política, e não serão revogados em resets voluntários de senha.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Vidas simbólicas com tokens de renovação de clientes públicos

Os clientes públicos não podem armazenar de forma segura uma palavra-passe do cliente (segredo). Por exemplo, uma aplicação iOS/Android não pode ocultar um segredo do proprietário do recurso, pelo que é considerado um cliente público. Pode definir políticas sobre recursos para evitar que os tokens de atualização de clientes públicos com mais de um período especificado obtenham um novo par de token de acesso/atualização. Para isso, utilize a [propriedade Refresh Token Max Inative Time](#refresh-token-max-inactive-time) `MaxInactiveTime` (). Também pode utilizar políticas para definir um período para além do qual os tokens de atualização já não são aceites. Para isso, utilize a propriedade [Single-Factor Refresh Token Max Age](#single-factor-session-token-max-age) ou a propriedade [Multi-Factor Session Token Max Age.](#multi-factor-refresh-token-max-age) Pode ajustar a vida útil de um token de atualização para controlar quando e com que frequência o utilizador é obrigado a reentrar em credenciais, em vez de ser silenciosamente reautentado, ao utilizar uma aplicação de cliente público.

A propriedade Max Age é o tempo que um único símbolo pode ser usado. 

### <a name="single-sign-on-session-tokens"></a>Fichas de sessão de inscrição única
Quando um utilizador autentica com a plataforma de identidade da Microsoft, é estabelecida uma única sessão de sessão (SSO) com o navegador do utilizador e a plataforma de identidade microsoft. O símbolo SSO, na forma de um cookie, representa esta sessão. O token de sessão SSO não está ligado a uma aplicação específica de recursos/cliente. Os tokens de sessão SSO podem ser revogados e a sua validade é verificada sempre que são utilizadas.

A plataforma de identidade da Microsoft utiliza dois tipos de fichas de sessão SSO: persistentes e não-persistentes. Tokens de sessão persistente são armazenados como cookies persistentes pelo navegador. As fichas de sessão não-escuta são armazenadas como cookies de sessão. (Os cookies de sessão são destruídos quando o navegador está fechado.) Normalmente, um token de sessão não-reservado é armazenado. Mas, quando o utilizador seleciona o **Keep me assinado na** caixa de verificação durante a autenticação, é armazenado um token de sessão persistente.

Fichas de sessão não-escutadas têm uma vida útil de 24 horas. Fichas persistentes têm uma vida de 90 dias. Sempre que um token de sessão SSO é utilizado dentro do seu período de validade, o período de validade é prorrogado por mais 24 horas ou 90 dias, dependendo do tipo de símbolo. Se um token de sessão SSO não for utilizado dentro do seu período de validade, é considerado caducado e já não é aceite.

Você pode usar uma política para definir o tempo após a primeira sessão token foi emitido para além do qual o token da sessão não é mais aceite. (Para isso, utilize a propriedade Session Token Max Age.) Pode ajustar a vida útil de um token de sessão para controlar quando e com que frequência um utilizador é obrigado a reentrar em credenciais, em vez de ser autenticado silenciosamente, quando utilizar uma aplicação web.

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Atualizar e session token propriedades políticas de vida
Uma política de vida simbólica é um tipo de objeto político que contém regras simbólicas de vida. Utilize as propriedades da apólice para controlar as vidas simbólicas especificadas. Se não for definida nenhuma política, o sistema aplica o valor de vida predefinido.

#### <a name="configurable-token-lifetime-properties"></a>Propriedades de vida simbólicas configuradas
| Propriedade | Cadeia de propriedade política | Afeta | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Atualizar tempo inativo token Max |MaxInactiveTime |Fichas de atualização |90 dias |10 minutos |90 dias |
| Single-Factor Refresh Token Max Age |MaxAgeSingleFactor |Fichas de atualização (para qualquer utilizadores) |Até revogação |10 minutos |Até revogado<sup>1</sup> |
| Multi-Factor Refresh Token Max Age |MaxAgeMultiFactor |Fichas de atualização (para qualquer utilizadores) | Até revogação |10 minutos |180 dias<sup>1</sup> |
| Single-Factor Session Token Max Age |MaxAgeSessionSingleFactor |Fichas de sessão (persistentes e não permanentes) |Até revogação |10 minutos |Até revogado<sup>1</sup> |
| Sessão multi-factor Token Max Age |MaxAgeSessionMultiFactor |Fichas de sessão (persistentes e não permanentes) | Até revogação |10 minutos | 180 dias<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento explícito máximo que pode ser definido para estes atributos.

#### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Predefinição |
| --- | --- | --- |
| Refresh Token Max Age (emitido para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |Fichas de atualização (emitidas para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Refresh Token Max Inative Time (emitido para clientes confidenciais) |Fichas de atualização (emitidas para clientes confidenciais) |90 dias |
| Refresh Token Max Age (emitido para clientes confidenciais) |Fichas de atualização (emitidas para clientes confidenciais) |Até revogação |

* <sup>1 Utilizadores</sup> federados que tenham informações de revogação insuficientes incluem quaisquer utilizadores que não tenham o atributo "LastPasswordChangeTimestamp" sincronizado. Estes utilizadores recebem esta curta Idade Max porque o Azure Ative Directory não consegue verificar quando revogar fichas que estão ligadas a uma credencial antiga (como uma palavra-passe que foi alterada) e devem voltar a fazer o check-in com mais frequência para garantir que o utilizador e os tokens associados ainda estão em boas condições. Para melhorar esta experiência, os administradores do arrendatário devem certificar-se de que estão a sincronizar o atributo "LastPasswordChangeTimestamp" (isto pode ser definido no objeto do utilizador utilizando o PowerShell ou através do AADSync).

### <a name="configurable-policy-property-details"></a>Detalhes de propriedade de política configurável

#### <a name="refresh-token-max-inactive-time"></a>Atualizar tempo inativo token Max
**Corda:** MaxInactiveTime

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla a idade de um token de atualização antes de um cliente não poder mais usá-lo para recuperar um novo par de token de acesso/atualização ao tentar aceder a este recurso. Como um novo token de atualização geralmente é devolvido quando um token de atualização é usado, esta política impede o acesso se o cliente tentar aceder a qualquer recurso usando o token de atualização atual durante o período de tempo especificado.

Esta política obriga os utilizadores que não tenham estado ativos no seu cliente a reauthenticar-se para recuperar um novo token de atualização.

A propriedade Refresh Token Max Inative Time deve ser definida para um valor inferior ao Single-Factor Token Max Age e as propriedades Multi-Factor Refresh Token Max Age.

Por exemplo, consulte [Criar uma política para uma aplicação nativa que chame uma API web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-refresh-token-max-age"></a>Single-Factor Refresh Token Max Age
**Corda:** MaxAgeSingleFactor

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de atualização para obter um novo par de token de acesso/atualização depois de terem sido autenticados com sucesso usando apenas um único fator. Depois de um utilizador autenticar e receber um novo token de atualização, o utilizador pode utilizar o fluxo de token de atualização durante o período de tempo especificado. (Isto é verdade desde que o token atual da atualização não seja revogado, e não seja deixado sem ser reutilizado por mais tempo do que o tempo inativo.) Nessa altura, o utilizador é obrigado a reautenticar-se para receber um novo token de atualização.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade para um valor igual ou inferior à propriedade Multi-Factor Refresh Token Max Age.

Por exemplo, consulte [Criar uma política para uma aplicação nativa que chame uma API web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor Refresh Token Max Age
**Corda:** MaxAgeMultiFactor

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de atualização para obter um novo par de token de acesso/atualização depois de terem sido autenticados com sucesso utilizando vários fatores. Depois de um utilizador autenticar e receber um novo token de atualização, o utilizador pode utilizar o fluxo de token de atualização durante o período de tempo especificado. (Isto é verdade desde que o token atual da atualização não seja revogado, e não seja desuso por mais tempo do que o tempo inativo.) Nessa altura, os utilizadores são obrigados a reauthenticar-se para receber um novo token de atualização.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade a um valor igual ou superior ao Single-Factor a propriedade Refresh Token Max Age.

Por exemplo, consulte [Criar uma política para uma aplicação nativa que chame uma API web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-session-token-max-age"></a>Single-Factor Session Token Max Age
**Corda:** MaxAgeSessionSingleFactor

**Afeta:** Fichas de sessão (persistentes e não permanentes)

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de sessão para obter um novo ID e token de sessão depois de terem sido autenticados com sucesso usando apenas um único fator. Depois de um utilizador autenticar e receber um novo token de sessão, o utilizador pode utilizar o fluxo de ficha de sessão durante o período de tempo especificado. (Isto é verdade desde que o token da sessão em curso não seja revogado e não tenha expirado.) Após o período de tempo especificado, o utilizador é obrigado a reauthenticar-se para receber um novo token de sessão.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade a um valor igual ou inferior à propriedade Multi-Factor Session Token Max Age.

Por exemplo, consulte [Criar uma política para iniciar sessão web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

#### <a name="multi-factor-session-token-max-age"></a>Sessão multi-factor Token Max Age
**Corda:** MaxAgeSessionMultiFactor

**Afeta:** Fichas de sessão (persistentes e não permanentes)

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de sessão para obter um novo ID e token de sessão após a última vez que autenticaram com sucesso usando vários fatores. Depois de um utilizador autenticar e receber um novo token de sessão, o utilizador pode utilizar o fluxo de ficha de sessão durante o período de tempo especificado. (Isto é verdade desde que o token da sessão em curso não seja revogado e não tenha expirado.) Após o período de tempo especificado, o utilizador é obrigado a reauthenticar-se para receber um novo token de sessão.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade a um valor igual ou superior ao Single-Factor sessão token max age.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Propriedades de vida simbólicas configuráveis após a reforma
A configuração de token de atualização e sessão é afetada pelas seguintes propriedades e pelos seus valores respectivamente definidos. Após a retirada da configuração de token de atualização e sessão em 30 de janeiro de 2021, a Azure AD apenas honrará os valores padrão descritos abaixo. Se decidir não utilizar o Acesso Condicional para gerir a frequência de inscrição, as suas fichas de atualização e sessão serão definidas como configuração padrão nessa data e deixará de poder alterar as suas vidas.  

|Propriedade   |Cadeia de propriedade política    |Afeta |Predefinição |
|----------|-----------|------------|------------|
|Access Token Lifetime |AccessTokenLifetime |Fichas de acesso, fichas de ID, fichas SAML2 |Uma hora |
|Atualizar tempo inativo token Max |MaxInactiveTime  |Fichas de atualização |90 dias  |
|Single-Factor Refresh Token Max Age  |MaxAgeSingleFactor  |Fichas de atualização (para qualquer utilizadores)  |Até revogação  |
|Multi-Factor Refresh Token Max Age  |MaxAgeMultiFactor  |Fichas de atualização (para qualquer utilizadores) |Até revogação  |
|Single-Factor Session Token Max Age  |MaxAgeSessionSingleFactor |Fichas de sessão (persistentes e não permanentes)  |Até revogação |
|Sessão multi-factor Token Max Age  |MaxAgeSessionMultiFactor  |Fichas de sessão (persistentes e não permanentes)  |Até revogação |

Pode usar o PowerShell para encontrar as políticas que serão afetadas pela reforma.  Use os [cmdlets PowerShell](configure-token-lifetimes.md#get-started) para ver todas as políticas criadas na sua organização ou para descobrir quais aplicações e diretores de serviço estão ligados a uma política específica.

## <a name="policy-evaluation-and-prioritization"></a>Avaliação política e priorização
Você pode criar e, em seguida, atribuir uma política de vida simbólica a uma aplicação específica, à sua organização e aos diretores de serviço. Várias políticas podem aplicar-se a uma aplicação específica. A política simbólica de vida vitalícia que entra em vigor segue estas regras:

* Se uma apólice for explicitamente atribuída ao diretor de serviço, é aplicada.
* Se nenhuma política for explicitamente atribuída ao diretor de serviço, é aplicada uma política explicitamente atribuída à organização-mãe do diretor de serviços.
* Se nenhuma política for explicitamente atribuída ao diretor de serviço ou à organização, a política atribuída ao pedido é aplicada.
* Se nenhuma política tiver sido atribuída ao principal de serviço, à organização ou ao objeto de aplicação, os valores predefinidos são aplicados. (Consulte a tabela em [propriedades de vida de símbolo configurado](#configurable-token-lifetime-properties-after-the-retirement).)

Para obter mais informações sobre a relação entre objetos de aplicação e objetos principais de serviço, consulte [aplicação e objetos principais de serviço no Diretório Ativo Azure](app-objects-and-service-principals.md).

A validade de um símbolo é avaliada no momento em que o token é usado. A política com maior prioridade na aplicação que está a ser acedida entra em vigor.

Todos os intervalos de tempo utilizados aqui são formatados de acordo com o objeto [C# TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Então, 80 dias e 30 minutos `80.00:30:00` seriam.  O D líder pode ser derrubado se zero, então 90 minutos seriam `00:90:00` .  

### <a name="example-scenario"></a>Cenário de exemplo

Um utilizador quer aceder a duas aplicações web: Aplicação Web A e Aplicação Web B.

Fatores:
* Ambas as aplicações web estão na mesma organização-mãe.
* Token Lifetime Policy 1 com uma Sessão Token Max Age de oito horas é definido como o padrão da organização-mãe.
* Web Application A é uma aplicação web de uso regular e não está ligada a nenhuma política.
* A Aplicação Web B é utilizada para processos altamente sensíveis. O seu diretor de serviço está ligado à Token Lifetime Policy 2, que tem uma Sessão Token Max Age de 30 minutos.

Às 12:00, o utilizador inicia uma nova sessão de navegador e tenta aceder à Aplicação Web A. O utilizador é redirecionado para a plataforma de identidade da Microsoft e é solicitado a iniciar seduca. Isto cria um cookie que tem um token de sessão no navegador. O utilizador é redirecionado de volta para a Aplicação Web A com um token de ID que permite ao utilizador aceder à aplicação.

Às 12:15, o utilizador tenta aceder à Aplicação Web B. O navegador redireciona para a plataforma de identidade da Microsoft, que deteta o cookie de sessão. O principal do serviço da Web Application B está ligado à Token Lifetime Policy 2, mas também faz parte da organização-mãe, com a Política de Vida Útil padrão 1. A Token Lifetime Policy 2 entra em vigor porque as políticas ligadas aos principais serviços têm uma prioridade maior do que as políticas de incumprimento da organização. O token da sessão foi originalmente emitido nos últimos 30 minutos, pelo que é considerado válido. O utilizador é redirecionado de volta para a Aplicação Web B com um token de ID que lhes dá acesso.

Às 13:00, o utilizador tenta aceder à Aplicação Web A. O utilizador é redirecionado para a plataforma de identidade da Microsoft. A Aplicação Web A não está ligada a nenhuma política, mas porque é numa organização com a Política de Vida Token 1 padrão, essa política entra em vigor. O cookie de sessão que foi originalmente emitido nas últimas oito horas é detetado. O utilizador é redirecionado silenciosamente para a Aplicação Web A com um novo token de ID. O utilizador não é obrigado a autenticar.

Imediatamente depois, o utilizador tenta aceder à Aplicação Web B. O utilizador é redirecionado para a plataforma de identidade da Microsoft. Como antes, a Token Lifetime Policy 2 entra em vigor. Como o símbolo foi emitido há mais de 30 minutos, o utilizador é solicitado a reentrar nas suas credenciais de inscrição. Um token de sessão novinho em folha e um símbolo de identificação são emitidos. O utilizador pode então aceder à Aplicação Web B.

## <a name="cmdlet-reference"></a>Referência de cmdlets

Estes são os cmdlets no [Azure Ative Directory PowerShell para o módulo de pré-visualização de gráficos](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Gerir políticas

Pode utilizar os seguintes cmdlets para gerir as políticas.

| Cmdlet | Descrição | 
| --- | --- |
| [Política new-AzureAD](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Cria uma nova política. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtém todas as políticas AD da Azure ou uma política especificada. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Obtém todas as aplicações e diretores de serviços que estão ligados a uma política. |
| [Política set-AzureAD](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Atualiza uma política existente. |
| [Remover-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Elimina a política especificada. |

### <a name="application-policies"></a>Políticas da aplicação
Pode utilizar os seguintes cmdlets para políticas de aplicação.</br></br>

| Cmdlet | Descrição | 
| --- | --- |
| [Add-AzureADApplicação Política](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Liga a política especificada a uma aplicação. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtém a apólice que é atribuída a uma aplicação. |
| [Remover-AzureADApplicaçãoPolição](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Remove uma apólice de uma aplicação. |

### <a name="service-principal-policies"></a>Políticas principais de serviços
Pode utilizar os seguintes cmdlets para as políticas principais do serviço.

| Cmdlet | Descrição | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Liga a política especificada a um diretor de serviço. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtém qualquer política ligada ao principal de serviço especificado.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Remove a política do principal de serviço especificado.|

## <a name="next-steps"></a>Próximos passos

Para saber mais, leia [exemplos de como configurar vidas simbólicas.](configure-token-lifetimes.md)
