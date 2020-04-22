---
title: Vida útil do token ad
titleSuffix: Microsoft identity platform
description: Aprenda a definir vidas para fichas emitidas pela Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4138c4ae24ae599d4058c9fd06c33b69657fe38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680077"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Toda a vida de fichas configuráveis em Diretório Ativo Azure (Pré-visualização)

Pode especificar a duração de um token emitido pelo Azure Active Directory (Azure AD). Pode definir durações de tokens para todas as aplicações existentes na sua organização, para uma aplicação multi-inquilino (com várias organizações) ou para um principal de serviço específico na sua organização.

> [!IMPORTANT]
> Depois de ouvir os clientes durante a pré-visualização, implementamos capacidades de gestão de sessões de [autenticação](https://go.microsoft.com/fwlink/?linkid=2083106) no Acesso Condicional Azure AD. Pode utilizar esta nova funcionalidade para configurar a atualização das vidas token, definindo o sinal na frequência. Depois de 1 de maio de 2020, não poderá utilizar a política configurável token lifetime para configurar sessão e refrescar fichas. Ainda pode configurar as vidas de acesso ao token após a depreciação.

Em Azure AD, um objeto político representa um conjunto de regras que são aplicadas em aplicações individuais ou em todas as aplicações de uma organização. Cada tipo de política tem uma estrutura única, com um conjunto de propriedades que são aplicadas a objetos aos quais são atribuídos.

Pode designar uma política como a política padrão para a sua organização. A política aplica-se a qualquer aplicação na organização, desde que não seja ultrapassada por uma política com maior prioridade. Também pode atribuir uma apólice a aplicações específicas. A ordem de prioridade varia consocada mente.

> [!NOTE]
> A política de vida token configurável só se aplica a clientes móveis e desktop que acedem ao SharePoint Online e oneDrive para recursos empresariais, e não se aplica às sessões do navegador web.
> Para gerir a vida útil das sessões de navegador web para O SharePoint Online e OneDrive para negócios, utilize a funcionalidade de vida vitalícia da sessão de [Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md) Consulte o [blog SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre a configuração de timeouts de sessão inativos.

## <a name="token-types"></a>Tipos de fichas

Você pode definir políticas de vida simbólicas para tokens de atualização, fichas de acesso, tokens SAML, tokens de sessão e tokens de id.

### <a name="access-tokens"></a>Tokens de acesso

Os clientes usam fichas de acesso para aceder a um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de utilizador, cliente e recurso. As fichas de acesso não podem ser revogadas e são válidas até ao seu termo. Um ator malicioso que obteve um sinal de acesso pode usá-lo durante toda a sua vida. Ajustar a vida útil de um token de acesso é uma compensação entre melhorar o desempenho do sistema e aumentar o tempo que o cliente mantém o acesso após a conta do utilizador ser desativada. O melhor desempenho do sistema é alcançado reduzindo o número de vezes que um cliente precisa para adquirir um novo sinal de acesso.  O predefinido é de 1 hora - após 1 hora, o cliente deve usar o token de atualização para (geralmente silenciosamente) adquirir um novo token de atualização e ficha de acesso. 

### <a name="saml-tokens"></a>Tokens SAML

As fichas SAML são usadas por muitas aplicações SAAS baseadas na web, e são obtidas usando o ponto final do protocolo SAML2 do Azure Ative Directory. São também consumidos por aplicações que utilizam a WS-Federação. A duração do token é de 1 hora. Do ponto de vista de uma aplicação, o período de validade do `<conditions …>` token é especificado pelo valor NotOnOrAfter do elemento no símbolo. Após o fim do período de validade do token, o cliente deve iniciar um novo pedido de autenticação, que muitas vezes será satisfeito sem inscrição interativa em resultado do token single sign on (SSO) Session.

O valor do NotOnOrAfter pode `AccessTokenLifetime` ser alterado `TokenLifetimePolicy`utilizando o parâmetro em . . Será definido para a vida útil configurado na apólice, se houver, mais um fator de rotação do relógio de cinco minutos.

Note que a confirmação do assunto `<SubjectConfirmationData>` NotOnOrAfter especificada no elemento não é afetada pela configuração Token Lifetime. 

### <a name="refresh-tokens"></a>Fichas de atualização

Quando um cliente adquire um sinal de acesso para aceder a um recurso protegido, o cliente também recebe um token de atualização. O token de atualização é usado para obter novos pares de acessórios de acesso/atualização quando o token de acesso atual expirar. Um token de atualização está ligado a uma combinação de utilizador e cliente. Um token de atualização pode ser [revogado](access-tokens.md#token-revocation)a qualquer momento , e a validade do token é verificada sempre que o símbolo é usado.  As fichas de atualização não são revogadas quando usadas para obter novos tokens de acesso - é a melhor prática, no entanto, para apagar seguramente o símbolo antigo ao obter um novo. 

É importante fazer uma distinção entre clientes confidenciais e clientes públicos, uma vez que isso afeta o tempo que os tokens de atualização podem ser usados. Para obter mais informações sobre diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token lifetimes com fichas confidenciais de atualização de cliente
Clientes confidenciais são aplicações que podem armazenar de forma segura uma palavra-passe do cliente (segredo). Podem provar que os pedidos vêm da aplicação de cliente segura e não de um ator malicioso. Por exemplo, uma aplicação web é um cliente confidencial porque pode armazenar um segredo de cliente no servidor web. Não está exposto. Uma vez que estes fluxos são mais seguros, as vidas `until-revoked`padrão das fichas de atualização emitidas a estes fluxos são , não podem ser alteradas usando a política, e não serão revogadas em resets voluntários de palavras-passe.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Token lifetimes com fichas de atualização de cliente público

Os clientes públicos não podem armazenar uma senha de cliente (segredo) de forma segura. Por exemplo, uma aplicação iOS/Android não pode ocultar um segredo do proprietário do recurso, pelo que é considerado um cliente público. Pode definir políticas sobre recursos para evitar que tokens de atualização de clientes públicos com mais de um período especificado obtenham um novo par de fichas de acesso/atualização. (Para isso, utilize a propriedade Refresh Token`MaxInactiveTime`Max Inative Time ().) Também pode utilizar políticas para definir um período para além do qual as fichas de atualização já não são aceites. (Para isso, utilize a propriedade Refresh Token Max Age.) Pode ajustar o tempo de vida de um token de atualização para controlar quando e com que frequência o utilizador é obrigado a reentrar nas credenciais, em vez de ser reautenticado silenciosamente, quando utilizar uma aplicação de cliente público.

> [!NOTE]
> A propriedade Max Age é o tempo que um único símbolo pode ser usado. 

### <a name="id-tokens"></a>Tokens de ID
Fichas de identificação são passadas para sites e clientes nativos. As fichas de identificação contêm informações de perfil sobre um utilizador. Um símbolo de identificação está ligado a uma combinação específica de utilizador e cliente. As fichas de identificação são consideradas válidas até ao seu termo. Normalmente, uma aplicação web corresponde ao tempo de vida útil da sessão de um utilizador na aplicação ao tempo de vida do token de id emitido para o utilizador. Pode ajustar o tempo de vida de um token de id para controlar a frequência com que a aplicação da Web expira na sessão de aplicação e com que frequência o utilizador é reautenticado com AD Azure (silenciosamente ou interativamente).

### <a name="single-sign-on-session-tokens"></a>Fichas únicas da sessão de inscrição
Quando um utilizador autentica com AD Azure, é estabelecida uma única sessão de sinalização (SSO) com o navegador do utilizador e a AD Azure. O símbolo SSO, na forma de um cookie, representa esta sessão. O token da sessão SSO não está ligado a uma aplicação específica de recursos/cliente. Os tokens da sessão SSO podem ser revogados, e a sua validade é verificada sempre que são usadas.

A Azure AD usa dois tipos de fichas de sessão SSO: persistente e não persistente. Os tokens de sessão persistentes são armazenados como cookies persistentes pelo navegador. Os tokens de sessão não persistentes são armazenados como cookies de sessão. (Os cookies de sessão são destruídos quando o navegador está fechado.) Normalmente, uma ficha de sessão não persistente é armazenada. Mas, quando o utilizador seleciona o **Keep me assinado na** caixa de verificação durante a autenticação, uma ficha de sessão persistente é armazenada.

Fichas de sessão não persistentes têm uma vida útil de 24 horas. Fichas persistentes têm uma vida inteira de 180 dias. Sempre que uma ficha de sessão SSO é utilizada dentro do seu período de validade, o período de validade é prorrogado mais 24 horas ou 180 dias, dependendo do tipo de token. Se uma ficha de sessão SSO não for utilizada dentro do seu período de validade, é considerada expirada e já não é aceite.

Pode utilizar uma política para definir o tempo após a primeira sessão ter sido emitida uma ficha para além da qual o token da sessão já não é aceite. (Para isso, utilize a propriedade Session Token Max Age.) Pode ajustar o tempo de vida de uma sessão para controlar quando e com que frequência um utilizador é obrigado a reentrar nas credenciais, em vez de ser silenciosamente autenticado, quando utilizar uma aplicação web.

### <a name="token-lifetime-policy-properties"></a>Propriedades de política vitalícia simbólica
Uma política simbólica de vida é um tipo de objeto político que contém regras de vida simbólicas. Utilize as propriedades da apólice para controlar as vidas token especificadas. Se nenhuma política for definida, o sistema aplica o valor de vida padrão.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de vida simbólicas configuráveis
| Propriedade | Cadeia de propriedade política | Afeta | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Acesso Token Lifetime |AccessTokenLifetime<sup>2</sup> |Fichas de acesso, fichas de identificação, fichas SAML2 |Uma hora |10 minutos |1 dia |
| Refrescar tempo inativo token Max |MaxInactiveTime |Fichas de atualização |90 dias |10 minutos |90 dias |
| Idade token max de atualização de um fator único |MaxageSingleFactor |Tokens de atualização (para qualquer utilizador) |Até que revogado |10 minutos |Até que revogado<sup>1</sup> |
| Multi-Factor Refresh Token Max Age |MaxageMultiFactor |Tokens de atualização (para qualquer utilizador) |Até que revogado |10 minutos |Até que revogado<sup>1</sup> |
| Idade token Max |MaxagesessionSingleFactor |Fichas de sessão (persistentes e não persistentes) |Até que revogado |10 minutos |Até que revogado<sup>1</sup> |
| Multi-Factor Session Token Max Age |MaxagesessionMultiFactor |Fichas de sessão (persistentes e não persistentes) |Até que revogado |10 minutos |Até que revogado<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento explícito máximo que pode ser definido para estes atributos.
* <sup>2</sup> Para garantir que o cliente Web da Microsoft Teams funciona, recomenda-se manter o AccessTokenLifetime a mais de 15 minutos para as Equipas Microsoft.

### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Predefinição |
| --- | --- | --- |
| Refresh Token Max Age (emitido para utilizadores federados que têm informações de revogação insuficientes<sup>1</sup>) |Fichas de atualização (emitidas para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Refresh Token Max Inative Time (emitido para clientes confidenciais) |Fichas de atualização (emitidas para clientes confidenciais) |90 dias |
| Refresh Token Max Age (emitido para clientes confidenciais) |Fichas de atualização (emitidas para clientes confidenciais) |Até que revogado |

* <sup>1</sup> Os utilizadores federados que tenham informações de revogação insuficientes incluem quaisquer utilizadores que não tenham o atributo "LastPasswordChangeTimestamp" sincronizado. Estes utilizadores recebem esta curta Idade Max porque a AAD não consegue verificar quando revogar fichas que estão ligadas a uma credencial antiga (como uma palavra-passe que foi alterada) e devem fazer o check-in com mais frequência para garantir que o utilizador e as fichas associadas ainda estão em boa posição. Para melhorar esta experiência, os administradores dos inquilinos devem certificar-se de que estão a sincronizar o atributo "LastPasswordChangeTimestamp" (isto pode ser definido no objeto de utilizador utilizando o Powershell ou através do AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Avaliação e priorização de políticas
Você pode criar e, em seguida, atribuir uma política de vida simbólica a uma aplicação específica, à sua organização, e aos diretores de serviço. Várias políticas podem aplicar-se a uma aplicação específica. A política simbólica vitalícia que entra em vigor segue estas regras:

* Se uma política for explicitamente atribuída ao diretor de serviço, é aplicada.
* Se nenhuma política for explicitamente atribuída ao diretor de serviço, é aplicada uma política explicitamente atribuída à organização-mãe do diretor de serviços.
* Se nenhuma política for explicitamente atribuída ao diretor de serviço ou à organização, a política atribuída à aplicação é aplicada.
* Se nenhuma política tiver sido atribuída ao diretor de serviço, à organização ou ao objeto de aplicação, os valores predefinidos são aplicados. (Ver a tabela em [propriedades de token configuráveis](#configurable-token-lifetime-properties).)

Para obter mais informações sobre a relação entre objetos de aplicação e objetos principais de serviço, consulte [os objetos principais da Aplicação e do serviço no Diretório Ativo do Azure](app-objects-and-service-principals.md).

A validade de um símbolo é avaliada no momento em que o símbolo é usado. A política com maior prioridade na aplicação a que está a ser acedida entra em vigor.

Todos os relógios utilizados aqui são formatados de acordo com o objeto C# [TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Então 80 dias e 30 minutos seriam. `80.00:30:00`  O líder D pode ser derrubado se zero, `00:90:00`então 90 minutos seriam .  

> [!NOTE]
> Aqui está um cenário de exemplo.
>
> Um utilizador quer aceder a duas aplicações web: Aplicação Web A e Aplicação Web B.
> 
> Fatores:
> * Ambas as aplicações web estão na mesma organização-mãe.
> * Token Lifetime Policy 1 com uma Sessão Token Max Age de oito horas é definido como o padrão da organização-mãe.
> * Aplicação Web A é uma aplicação web de uso regular e não está ligada a nenhuma política.
> * Aplicação Web B é usado para processos altamente sensíveis. O seu diretor de serviço está ligado à Token Lifetime Policy 2, que tem uma Sessão Token Max Age de 30 minutos.
>
> Às 12:00, o utilizador inicia uma nova sessão de navegador e tenta aceder à Aplicação Web A. O utilizador é redirecionado para a AD Azure e é solicitado a iniciar sessão. Isto cria um cookie que tem um token de sessão no navegador. O utilizador é redirecionado para a Aplicação Web A com um token DE IDENTIFICAção que permite ao utilizador aceder à aplicação.
>
> Às 12:15, o utilizador tenta aceder à Aplicação Web B. O navegador redireciona para a AD Azure, que deteta o cookie da sessão. O diretor de serviço da Aplicação Web B está ligado à Token Lifetime Policy 2, mas também faz parte da organização-mãe, com a Política vitalícia padrão token 1. Token Lifetime Policy 2 entra em vigor porque as políticas ligadas aos principais de serviços têm uma prioridade maior do que as políticas de incumprimento da organização. O token da sessão foi originalmente emitido nos últimos 30 minutos, pelo que é considerado válido. O utilizador é redirecionado para a Aplicação Web B com um token de identificação que lhes dá acesso.
>
> Às 13:00, o utilizador tenta aceder à Aplicação Web A. O utilizador é redirecionado para a AD Azure. Aplicação Web A não está ligado a nenhuma política, mas porque está numa organização com política de vida padrão token 1, essa política entra em vigor. O cookie da sessão que foi originalmente emitido nas últimas oito horas é detetado. O utilizador é redirecionado silenciosamente para a Aplicação Web A com um novo token ID. O utilizador não é obrigado a autenticar.
>
> Imediatamente a seguir, o utilizador tenta aceder à Aplicação Web B. O utilizador é redirecionado para a AD Azure. Como antes, token Life Policy 2 entra em vigor. Como o símbolo foi emitido há mais de 30 minutos, o utilizador é solicitado a reentrar nas suas credenciais de entrada. Um novo token de sessão e ficha de identificação são emitidos. O utilizador pode então aceder à Aplicação Web B.
>
>

## <a name="configurable-policy-property-details"></a>Detalhes da propriedade de política configurável
### <a name="access-token-lifetime"></a>Acesso Token Lifetime
**Cadeia:** AccessTokenLifetime

**Afeta:** Fichas de acesso, fichas de identificação, fichas SAML

**Resumo:** Esta política controla a duração do acesso e dos tokens de identificação deste recurso são considerados válidos. A redução da propriedade Access Token Lifetime atenua o risco de um token de acesso ou ficha de identificação ser usado por um ator malicioso por um longo período de tempo. (Estas fichas não podem ser revogadas.) A compensação é que o desempenho é negativamente afetado, porque as fichas têm de ser substituídas com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Refrescar tempo inativo token Max
**Cadeia:** MaxInactiveTime

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla a idade de um token de atualização antes que um cliente não possa mais usá-lo para recuperar um novo par de fichas de acesso/atualização ao tentar aceder a este recurso. Uma vez que um novo token de atualização é normalmente devolvido quando um token de atualização é usado, esta política impede o acesso se o cliente tentar aceder a qualquer recurso usando o token de atualização atual durante o período de tempo especificado.

Esta política obriga os utilizadores que não têm estado ativos no seu cliente a reautenticar em recuperar um novo token de atualização.

A propriedade Refresh Token Max Inative Time deve ser definida para um valor mais baixo do que a Idade Máxima token single-factor e as propriedades de Multi-Factor Refresh Token Max Age.

### <a name="single-factor-refresh-token-max-age"></a>Idade token max de atualização de um fator único
**Cadeia:** MaxageSingleFactor

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de atualização para obter um novo par de fichas de acesso/atualização depois de ter autenticado com sucesso usando apenas um único fator. Depois de um utilizador autenticar e receber um novo token de atualização, o utilizador pode utilizar o fluxo de token de atualização durante o período de tempo especificado. (Isto é verdade desde que o token de atualização atual não seja revogado, e não seja deixado por utilizar por mais tempo do que o tempo inativo.) Nessa altura, o utilizador é obrigado a reautenticar para receber um novo token de atualização.

A redução da idade máxima obriga os utilizadores a autenticar com mais frequência. Uma vez que a autenticação de um fator único é considerada menos segura do que a autenticação de vários fatores, recomendamos que este imóvel seja igual ou inferior ao da propriedade Multi-Factor Refresh Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor Refresh Token Max Age
**Cadeia:** MaxageMultiFactor

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de atualização para obter um novo par de fichas de acesso/atualização depois de ter autenticado com sucesso usando vários fatores. Depois de um utilizador autenticar e receber um novo token de atualização, o utilizador pode utilizar o fluxo de token de atualização durante o período de tempo especificado. (Isto é verdade desde que o token de atualização atual não seja revogado, e não seja utilizado por mais tempo do que o tempo inativo.) Nessa altura, os utilizadores são obrigados a reautenticar para receber um novo token de atualização.

A redução da idade máxima obriga os utilizadores a autenticar com mais frequência. Uma vez que a autenticação de um fator único é considerada menos segura do que a autenticação de vários fatores, recomendamos que este imóvel seja igual ou superior ao imóvel De atualização de fator único Token Max Age.

### <a name="single-factor-session-token-max-age"></a>Idade token Max
**Cadeia:** MaxagesessionSingleFactor

**Afeta:** Fichas de sessão (persistentes e não persistentes)

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de sessão para obter um novo documento de identificação e sessão depois de ter autenticado com sucesso usando apenas um único fator. Depois de um utilizador autenticar e receber uma nova sessão de token, o utilizador pode utilizar o fluxo de fichas da sessão durante o período de tempo especificado. (Isto é verdade desde que o presente token da sessão não seja revogado e não tenha expirado.) Após o período de tempo especificado, o utilizador é obrigado a reautenticar para receber um novo token de sessão.

A redução da idade máxima obriga os utilizadores a autenticar com mais frequência. Uma vez que a autenticação de um fator único é considerada menos segura do que a autenticação de vários fatores, recomendamos que este imóvel seja igual ou inferior ao da propriedade Multi-Factor Session Token Max Age.

### <a name="multi-factor-session-token-max-age"></a>Multi-Factor Session Token Max Age
**Cadeia:** MaxagesessionMultiFactor

**Afeta:** Fichas de sessão (persistentes e não persistentes)

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de sessão para obter um novo documento de identificação e sessão após a última vez que autenticaram com sucesso usando vários fatores. Depois de um utilizador autenticar e receber uma nova sessão de token, o utilizador pode utilizar o fluxo de fichas da sessão durante o período de tempo especificado. (Isto é verdade desde que o presente token da sessão não seja revogado e não tenha expirado.) Após o período de tempo especificado, o utilizador é obrigado a reautenticar para receber um novo token de sessão.

A redução da idade máxima obriga os utilizadores a autenticar com mais frequência. Uma vez que a autenticação de um fator único é considerada menos segura do que a autenticação de vários fatores, recomendamos que este imóvel seja igual ou superior ao imóvel token Max Age da Sessão Single-Factor.

## <a name="example-token-lifetime-policies"></a>Exemplo de políticas de vida simbólicas
Muitos cenários são possíveis em Azure AD quando você pode criar e gerir vidas simbólicas para apps, diretores de serviço e sua organização geral. Nesta secção, percorremos alguns cenários de política comum que podem ajudá-lo a impor novas regras para:

* Duração do Token
* Tempo Inativo Token Max
* Idade Token Max

Nos exemplos, pode aprender a:

* Gerir a política de incumprimento de uma organização
* Criar uma política de inscrição na web
* Crie uma política para uma aplicação nativa que chama a API web
* Gerir uma política avançada

### <a name="prerequisites"></a>Pré-requisitos
Nos seguintes exemplos, cria, atualiza, liga e elimina políticas para apps, diretores de serviços e a sua organização em geral. Se você é novo em Azure AD, recomendamos que você aprenda sobre [como obter um inquilino Azure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.  

Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento do Módulo PowerShell Da [AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Execute `Connect` o comando para iniciar sessão na sua conta de administração da AD Azure. Execute este comando sempre que iniciar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas que foram criadas na sua organização, executar o seguinte comando. Executar este comando após a maioria das operações nos seguintes cenários. Executar o comando também ajuda a obter o ** ** das suas políticas.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerir a política de incumprimento de uma organização
Neste exemplo, cria uma política que permite o sinal dos seus utilizadores com menos frequência em toda a sua organização. Para isso, crie uma política de vida simbólica para tokens de refrescamento single-factor, que é aplicado em toda a sua organização. A política é aplicada a todas as aplicações da sua organização, e a cada diretor de serviço que ainda não tem um conjunto de políticas.

1. Crie uma política simbólica para toda a vida.

    1. Desloque o token de atualização de fator único para "até revogar". O símbolo não expira até que o acesso seja revogado. Criar a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Para criar a política, executar o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para remover qualquer espaço branco, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Para ver a sua nova política, e para obter o **ObjectId**da apólice, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atualize a apólice.

    Pode decidir que a primeira política que deu neste exemplo não é tão rigorosa como o seu serviço exige. Para definir o seu Token Refresh Single-Factor expirar em dois dias, execute o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política de inscrição na Web

Neste exemplo, cria-se uma política que exige que os utilizadores autentiquem com mais frequência na sua aplicação web. Esta política define a vida útil dos tokens de acesso/ID e a idade máxima de uma sessão de vários fatores para o diretor de serviço da sua aplicação web.

1. Crie uma política simbólica para toda a vida.

    Esta política, para o início de sessão web, define a vida útil do token de acesso/ID e a idade máxima da sessão de um único fator para duas horas.

    1. Para criar a política, executar este comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, e para obter a política **ObjectId,** executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o **ObjectId** do seu diretor de serviço.

    1. Utilize o [Cmdlet Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todos os diretores de serviço da sua organização ou um único diretor de serviço.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Quando tiver o diretor de serviço, faça o seguinte comando:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para uma aplicação nativa que chama a API web
Neste exemplo, cria-se uma política que exige que os utilizadores autentiquem com menos frequência. A política também alonga o tempo que um utilizador pode estar inativo antes de o utilizador se reanotar. A política é aplicada à Web API. Quando a aplicação nativa solicita a API web como recurso, esta política é aplicada.

1. Crie uma política simbólica para toda a vida.

    1. Para criar uma política rigorosa para uma API web, executar o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, dirija o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice à sua Web API. Também precisa de obter o **ObjectId** da sua aplicação. Utilize o cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para encontrar o **ObjectId**da sua aplicação, ou utilize o [portal Azure](https://portal.azure.com/).

    Obtenha o **ObjectId** da sua aplicação e atribua a política:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerir uma política avançada
Neste exemplo, cria-se algumas políticas para aprender como funciona o sistema prioritário. Você também aprende como gerir múltiplas políticas que são aplicadas a vários objetos.

1. Crie uma política simbólica para toda a vida.

    1. Para criar uma política de incumprimento da organização que define a vida útil do Single-Factor Refresh Token para 30 dias, executar o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, dirija o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribuir a apólice a um diretor de serviço.

    Agora, tem uma política que se aplica a toda a organização. Talvez queira preservar esta política de 30 dias para um diretor de serviço específico, mas mude a política de incumprimento da organização para o limite máximo de "até revogar".

    1. Para ver todos os diretores de serviço da sua organização, utilize o [Cmdlet Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    1. Quando tiver o diretor de serviço, faça o seguinte comando:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Coloque `IsOrganizationDefault` a bandeira em falso:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Criar uma nova política de incumprimento da organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Tem agora a política original ligada ao seu diretor de serviço, e a nova política é definida como a sua política de incumprimento da organização. É importante lembrar que as políticas aplicadas aos diretores de serviçotêm prioridade sobre as políticas de incumprimento da organização.

## <a name="cmdlet-reference"></a>Referência de cmdlets

### <a name="manage-policies"></a>Gerir políticas

Pode utilizar os seguintes cmdlets para gerir as políticas.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Cria uma nova política.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Uma série de JSON stringified que contém todas as regras da política. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Cadeia do nome da apólice. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se for verdade, define a política como a política padrão da organização. Se é falso, não faz nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo de política. Para token lifetimes, use sempre "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcional] |Estabelece uma identificação alternativa para a apólice. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as políticas da AD Azure ou uma política especificada.

```powershell
Get-AzureADPolicy
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code>[Opcional] |**ObjectId (ID)** da política que deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todas as aplicações e diretores de serviço que estão ligados a uma política.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Atualiza uma política existente.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que deseja. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Cadeia do nome da apólice. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Opcional] |Uma série de JSON stringified que contém todas as regras da política. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Opcional] |Se for verdade, define a política como a política padrão da organização. Se é falso, não faz nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Opcional] |Tipo de política. Para token lifetimes, use sempre "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcional] |Estabelece uma identificação alternativa para a apólice. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remover-AzureADPolicy
Elimina a política especificada.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que deseja. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Políticas da aplicação
Pode utilizar os seguintes cmdlets para as políticas de aplicação.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Política de Aplicação Add-AzureAD
Liga a política especificada a uma aplicação.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da apólice. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Política de Aplicações Get-AzureAD
Obtém a política que é atribuída a uma candidatura.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Política de Aplicação remove-AzureAD
Remove uma apólice de uma aplicação.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da apólice. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas principais de serviço
Pode utilizar os seguintes cmdlets para as políticas principais de serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>Política principal add-AzureADService
Liga a política especificada a um diretor de serviço.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da apólice. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Política principal do Get-AzureADService
Obtém qualquer política ligada ao diretor de serviço especificado.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remover-AzureADServicePrincipalPolicy
Remove a apólice do diretor de serviço especificado.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da apólice. | `-PolicyId <ObjectId of Policy>` |
