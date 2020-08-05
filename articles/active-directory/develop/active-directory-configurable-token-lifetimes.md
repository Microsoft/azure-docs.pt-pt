---
title: Vidas simbólicas configuráveis
titleSuffix: Microsoft identity platform
description: Saiba como definir vidas para fichas emitidas pela plataforma de identidade microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: d25c2e2603f36ff090d01f235a4c8e4a1ae12605
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552854"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Vidas de token configuradas na plataforma de identidade da Microsoft (Preview)

Pode especificar a vida útil de um símbolo emitido pela plataforma de identidade Microsoft. Pode definir durações de tokens para todas as aplicações existentes na sua organização, para uma aplicação multi-inquilino (com várias organizações) ou para um principal de serviço específico na sua organização.

> [!IMPORTANT]
> Depois de ouvir os clientes durante a pré-visualização, implementamos [capacidades de gestão de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106) no Azure AD Conditional Access. Pode utilizar esta nova funcionalidade para configurar as vidas de token de atualização, definindo o sinal na frequência. Depois de 30 de maio de 2020 nenhum novo inquilino poderá usar a política de Vida Token Configural para configurar sessão e refrescar tokens. A depreciação acontecerá dentro de alguns meses, o que significa que deixaremos de honrar a sessão existente e refrescaremos as polícias. Ainda pode configurar o acesso a vidas simbólicas após a depreciação.

Em Azure AD, um objeto de política representa um conjunto de regras que são aplicadas em aplicações individuais ou em todas as aplicações de uma organização. Cada tipo de política tem uma estrutura única, com um conjunto de propriedades que são aplicadas a objetos aos quais são atribuídos.

Pode designar uma política como a política padrão para a sua organização. A política é aplicada a qualquer aplicação na organização, desde que não seja ultrapassada por uma política com maior prioridade. Também pode atribuir uma apólice a aplicações específicas. A ordem de prioridade varia consoam em comparação com o tipo de política.

> [!NOTE]
> A política de vida de token configurada aplica-se apenas a clientes móveis e desktop que acedam ao SharePoint Online e ao OneDrive para recursos empresariais, e não se aplicam a sessões de navegador da Web.
> Para gerir o tempo de vida útil das sessões de navegador web para SharePoint Online e OneDrive para negócios, utilize a funcionalidade de vida útil da [sessão de acesso condicional.](../conditional-access/howto-conditional-access-session-lifetime.md) Consulte o [blog SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre configurar os intervalos de sessão ocioso.

## <a name="token-types"></a>Tipos de token

Você pode definir políticas de vida útil para tokens de atualização, tokens de acesso, fichas SAML, fichas de sessão e fichas de identificação.

### <a name="access-tokens"></a>Tokens de acesso

Os clientes usam fichas de acesso para aceder a um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de utilizador, cliente e recurso. As fichas de acesso não podem ser revogadas e são válidas até ao seu termo. Um ator malicioso que obteve um símbolo de acesso pode usá-lo para a extensão da sua vida. Ajustar a vida útil de um token de acesso é uma compensação entre melhorar o desempenho do sistema e aumentar o tempo que o cliente mantém o acesso após a desativação da conta do utilizador. O melhor desempenho do sistema é alcançado reduzindo o número de vezes que um cliente precisa para adquirir um novo token de acesso.  O padrão é de 1 hora - após 1 hora, o cliente deve usar o token de atualização para (geralmente silenciosamente) adquirir um novo token de atualização e ficha de acesso. 

### <a name="saml-tokens"></a>Tokens SAML

Os tokens SAML são usados por muitas aplicações SAAS baseadas na web, e são obtidos usando o ponto final do protocolo SAML2 do Azure Ative Directory. Também são consumidos por aplicações que utilizam a WS-Federation. A vida útil padrão do símbolo é de 1 hora. Do ponto de vista da aplicação, o período de validade do token é especificado pelo valor notOnOrAfter do `<conditions …>` elemento no token. Após o fim do período de validade do token, o cliente deve iniciar um novo pedido de autenticação, que muitas vezes será satisfeito sem o sinal interativo como resultado do token de Sessão de Sinal Único (SSO).

O valor de NotOnOrAfter pode ser alterado usando o `AccessTokenLifetime` parâmetro num `TokenLifetimePolicy` . Será definido para a vida útil configurada na apólice, se houver, mais um fator de distorção do relógio de cinco minutos.

Note que a confirmação do assunto NotOnOrAfter especificada no `<SubjectConfirmationData>` elemento não é afetada pela configuração Token Lifetime. 

### <a name="refresh-tokens"></a>Fichas de atualização

Quando um cliente adquire um token de acesso para aceder a um recurso protegido, o cliente também recebe um token de atualização. O token refresh é usado para obter novos pares de token de acesso/atualização quando o token de acesso atual expirar. Um token de atualização está ligado a uma combinação de utilizador e cliente. Um token de atualização pode ser [revogado a qualquer momento](access-tokens.md#token-revocation), e a validade do token é verificada sempre que o token é usado.  Os tokens refresh não são revogados quando usados para obter novos tokens de acesso - é melhor prática, no entanto, apagar de forma segura o velho token quando se consegue um novo. 

É importante fazer uma distinção entre clientes confidenciais e clientes públicos, pois isso tem impacto no tempo de utilização de tokens de atualização. Para obter mais informações sobre diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Vidas simbólicas com fichas confidenciais de atualização de clientes
Os clientes confidenciais são aplicações que podem armazenar de forma segura uma palavra-passe do cliente (segredo). Podem provar que os pedidos vêm da aplicação de clientes seguros e não de um ator malicioso. Por exemplo, uma aplicação web é um cliente confidencial porque pode armazenar um segredo de cliente no servidor web. Não está exposto. Como estes fluxos são mais seguros, as vidas padrão dos tokens de atualização emitidos para estes fluxos `until-revoked` são , não podem ser alterados usando a política, e não serão revogados em resets voluntários de senha.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Vidas simbólicas com tokens de renovação de clientes públicos

Os clientes públicos não podem armazenar de forma segura uma palavra-passe do cliente (segredo). Por exemplo, uma aplicação iOS/Android não pode ocultar um segredo do proprietário do recurso, pelo que é considerado um cliente público. Pode definir políticas sobre recursos para evitar que os tokens de atualização de clientes públicos com mais de um período especificado obtenham um novo par de token de acesso/atualização. (Para isso, utilize a propriedade Refresh Token Max Inative Time `MaxInactiveTime` ().) Também pode utilizar políticas para definir um período para além do qual os tokens de atualização já não são aceites. (Para isso, utilize a propriedade Refresh Token Max Age.) Pode ajustar a vida útil de um token de atualização para controlar quando e com que frequência o utilizador é obrigado a reentrar em credenciais, em vez de ser silenciosamente reautentado, ao utilizar uma aplicação de cliente público.

> [!NOTE]
> A propriedade Max Age é o tempo que um único símbolo pode ser usado. 

### <a name="id-tokens"></a>Tokens de ID
As fichas de identificação são passadas para websites e clientes nativos. As fichas de identificação contêm informações de perfil sobre um utilizador. Um símbolo de identificação está ligado a uma combinação específica de utilizador e cliente. As fichas de identificação são consideradas válidas até ao seu termo. Normalmente, uma aplicação web corresponde à vida útil de um utilizador na aplicação ao tempo de vida útil do token de ID emitido para o utilizador. Pode ajustar o tempo de vida útil de um token de ID para controlar a frequência com que a aplicação web expira na sessão de aplicação, e com que frequência requer que o utilizador seja reauthentado com a plataforma de identidade da Microsoft (silenciosamente ou interativamente).

### <a name="single-sign-on-session-tokens"></a>Fichas de sessão de inscrição única
Quando um utilizador autentica com a plataforma de identidade da Microsoft, é estabelecida uma única sessão de sessão de sessão de sessão de sessão de sessão de sessão (SSO) com o navegador do utilizador e a plataforma de identidade microsoft. O símbolo SSO, na forma de um cookie, representa esta sessão. O token de sessão SSO não está ligado a uma aplicação específica de recursos/cliente. Os tokens de sessão SSO podem ser revogados e a sua validade é verificada sempre que são utilizadas.

A plataforma de identidade da Microsoft utiliza dois tipos de fichas de sessão SSO: persistentes e não-persistentes. Tokens de sessão persistente são armazenados como cookies persistentes pelo navegador. As fichas de sessão não-escuta são armazenadas como cookies de sessão. (Os cookies de sessão são destruídos quando o navegador está fechado.) Normalmente, um token de sessão não-reservado é armazenado. Mas, quando o utilizador seleciona o **Keep me assinado na** caixa de verificação durante a autenticação, é armazenado um token de sessão persistente.

Fichas de sessão não-escutadas têm uma vida útil de 24 horas. Fichas persistentes têm uma vida de 90 dias. Sempre que um token de sessão SSO é utilizado dentro do seu período de validade, o período de validade é prorrogado por mais 24 horas ou 90 dias, dependendo do tipo de símbolo. Se um token de sessão SSO não for utilizado dentro do seu período de validade, é considerado caducado e já não é aceite.

Você pode usar uma política para definir o tempo após a primeira sessão token foi emitido para além do qual o token da sessão não é mais aceite. (Para isso, utilize a propriedade Session Token Max Age.) Pode ajustar a vida útil de um token de sessão para controlar quando e com que frequência um utilizador é obrigado a reentrar em credenciais, em vez de ser autenticado silenciosamente, quando utilizar uma aplicação web.

### <a name="token-lifetime-policy-properties"></a>Propriedades políticas de vida útil token
Uma política de vida simbólica é um tipo de objeto político que contém regras simbólicas de vida. Utilize as propriedades da apólice para controlar as vidas simbólicas especificadas. Se não for definida nenhuma política, o sistema aplica o valor de vida predefinido.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de vida simbólicas configuradas
| Propriedade | Cadeia de propriedade política | Afeta | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Access Token Lifetime |AccessTokenLifetime<sup>2</sup> |Fichas de acesso, fichas de ID, fichas SAML2 |Uma hora |10 minutos |1 dia |
| Atualizar tempo inativo token Max |MaxInactiveTime |Fichas de atualização |90 dias |10 minutos |90 dias |
| Single-Factor Refresh Token Max Age |MaxAgeSingleFactor |Fichas de atualização (para qualquer utilizadores) |Até revogação |10 minutos |Até revogado<sup>1</sup> |
| Multi-Factor Refresh Token Max Age |MaxAgeMultiFactor |Fichas de atualização (para qualquer utilizadores) |Até revogação |10 minutos |Até revogado<sup>1</sup> |
| Sessão de fator único Token Max Age |MaxAgeSessionSingleFactor |Fichas de sessão (persistentes e não permanentes) |Até revogação |10 minutos |Até revogado<sup>1</sup> |
| Sessão multi-factor Token Max Age |MaxAgeSessionMultiFactor |Fichas de sessão (persistentes e não permanentes) |Até revogação |10 minutos |Até revogado<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento explícito máximo que pode ser definido para estes atributos.
* <sup>2</sup> Para garantir que o cliente Web da Microsoft Teams funciona, recomenda-se manter o AccessTokenLifetime a mais de 15 minutos para as Equipas microsoft.

### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Predefinição |
| --- | --- | --- |
| Refresh Token Max Age (emitido para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |Fichas de atualização (emitidas para utilizadores federados que tenham informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Refresh Token Max Inative Time (emitido para clientes confidenciais) |Fichas de atualização (emitidas para clientes confidenciais) |90 dias |
| Refresh Token Max Age (emitido para clientes confidenciais) |Fichas de atualização (emitidas para clientes confidenciais) |Até revogação |

* <sup>1 Utilizadores</sup> federados que tenham informações de revogação insuficientes incluem quaisquer utilizadores que não tenham o atributo "LastPasswordChangeTimestamp" sincronizado. Estes utilizadores recebem esta curta Idade Max porque a AAD não consegue verificar quando revogar tokens que estão ligados a uma credencial antiga (como uma palavra-passe que foi alterada) e devem voltar a fazer o check-in com mais frequência para garantir que o utilizador e os tokens associados ainda estão em boas condições. Para melhorar esta experiência, os administradores do arrendatário devem certificar-se de que estão a sincronizar o atributo "LastPasswordChangeTimestamp" (isto pode ser definido no objeto do utilizador utilizando o PowerShell ou através do AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Avaliação política e priorização
Você pode criar e, em seguida, atribuir uma política de vida simbólica a uma aplicação específica, à sua organização e aos diretores de serviço. Várias políticas podem aplicar-se a uma aplicação específica. A política simbólica de vida vitalícia que entra em vigor segue estas regras:

* Se uma apólice for explicitamente atribuída ao diretor de serviço, é aplicada.
* Se nenhuma política for explicitamente atribuída ao diretor de serviço, é aplicada uma política explicitamente atribuída à organização-mãe do diretor de serviços.
* Se nenhuma política for explicitamente atribuída ao diretor de serviço ou à organização, a política atribuída ao pedido é aplicada.
* Se nenhuma política tiver sido atribuída ao principal de serviço, à organização ou ao objeto de aplicação, os valores predefinidos são aplicados. (Consulte a tabela em [propriedades de vida de símbolo configurado](#configurable-token-lifetime-properties).)

Para obter mais informações sobre a relação entre objetos de aplicação e objetos principais de serviço, consulte [aplicação e objetos principais de serviço no Diretório Ativo Azure](app-objects-and-service-principals.md).

A validade de um símbolo é avaliada no momento em que o token é usado. A política com maior prioridade na aplicação que está a ser acedida entra em vigor.

Todos os intervalos de tempo utilizados aqui são formatados de acordo com o objeto [C# TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Então, 80 dias e 30 minutos `80.00:30:00` seriam.  O D líder pode ser derrubado se zero, então 90 minutos seriam `00:90:00` .  

> [!NOTE]
> Aqui está um cenário de exemplo.
>
> Um utilizador quer aceder a duas aplicações web: Aplicação Web A e Aplicação Web B.
> 
> Fatores:
> * Ambas as aplicações web estão na mesma organização-mãe.
> * Token Lifetime Policy 1 com uma Sessão Token Max Age de oito horas é definido como o padrão da organização-mãe.
> * Web Application A é uma aplicação web de uso regular e não está ligada a nenhuma política.
> * A Aplicação Web B é utilizada para processos altamente sensíveis. O seu diretor de serviço está ligado à Token Lifetime Policy 2, que tem uma Sessão Token Max Age de 30 minutos.
>
> Às 12:00, o utilizador inicia uma nova sessão de navegador e tenta aceder à Aplicação Web A. O utilizador é redirecionado para a plataforma de identidade da Microsoft e é solicitado a iniciar seduca. Isto cria um cookie que tem um token de sessão no navegador. O utilizador é redirecionado de volta para a Aplicação Web A com um token de ID que permite ao utilizador aceder à aplicação.
>
> Às 12:15, o utilizador tenta aceder à Aplicação Web B. O navegador redireciona para a plataforma de identidade da Microsoft, que deteta o cookie de sessão. O principal do serviço da Web Application B está ligado à Token Lifetime Policy 2, mas também faz parte da organização-mãe, com a Política de Vida Útil padrão 1. A Token Lifetime Policy 2 entra em vigor porque as políticas ligadas aos principais serviços têm uma prioridade maior do que as políticas de incumprimento da organização. O token da sessão foi originalmente emitido nos últimos 30 minutos, pelo que é considerado válido. O utilizador é redirecionado de volta para a Aplicação Web B com um token de ID que lhes dá acesso.
>
> Às 13:00, o utilizador tenta aceder à Aplicação Web A. O utilizador é redirecionado para a plataforma de identidade da Microsoft. A Aplicação Web A não está ligada a nenhuma política, mas porque é numa organização com a Política de Vida Token 1 padrão, essa política entra em vigor. O cookie de sessão que foi originalmente emitido nas últimas oito horas é detetado. O utilizador é redirecionado silenciosamente para a Aplicação Web A com um novo token de ID. O utilizador não é obrigado a autenticar.
>
> Imediatamente depois, o utilizador tenta aceder à Aplicação Web B. O utilizador é redirecionado para a plataforma de identidade da Microsoft. Como antes, a Token Lifetime Policy 2 entra em vigor. Como o símbolo foi emitido há mais de 30 minutos, o utilizador é solicitado a reentrar nas suas credenciais de inscrição. Um token de sessão novinho em folha e um símbolo de identificação são emitidos. O utilizador pode então aceder à Aplicação Web B.
>
>

## <a name="configurable-policy-property-details"></a>Detalhes de propriedade de política configurável
### <a name="access-token-lifetime"></a>Access Token Lifetime
**Corda:** AccessTokenLifetime

**Afeta:** Fichas de acesso, fichas de ID, fichas SAML

**Resumo:** Esta política controla a duração do acesso e as fichas de identificação deste recurso são consideradas válidas. A redução da propriedade Access Token Lifetime atenua o risco de um token de acesso ou símbolo de ID ser usado por um ator malicioso por um longo período de tempo. (Estas fichas não podem ser revogadas.) A compensação é que o desempenho é afetado negativamente, porque os tokens têm de ser substituídos com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Atualizar tempo inativo token Max
**Corda:** MaxInactiveTime

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla a idade de um token de atualização antes de um cliente não poder mais usá-lo para recuperar um novo par de token de acesso/atualização ao tentar aceder a este recurso. Como um novo token de atualização geralmente é devolvido quando um token de atualização é usado, esta política impede o acesso se o cliente tentar aceder a qualquer recurso usando o token de atualização atual durante o período de tempo especificado.

Esta política obriga os utilizadores que não tenham estado ativos no seu cliente a reauthenticar-se para recuperar um novo token de atualização.

A propriedade Refresh Token Max Inative Time deve ser definida para um valor inferior ao Single-Factor Token Max Age e às propriedades Multi-Factor Refresh Token Max Age.

### <a name="single-factor-refresh-token-max-age"></a>Single-Factor Refresh Token Max Age
**Corda:** MaxAgeSingleFactor

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de atualização para obter um novo par de token de acesso/atualização depois de terem sido autenticados com sucesso usando apenas um único fator. Depois de um utilizador autenticar e receber um novo token de atualização, o utilizador pode utilizar o fluxo de token de atualização durante o período de tempo especificado. (Isto é verdade desde que o token atual da atualização não seja revogado, e não seja deixado sem ser reutilizado por mais tempo do que o tempo inativo.) Nessa altura, o utilizador é obrigado a reautenticar-se para receber um novo token de atualização.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade para um valor igual ou inferior à propriedade Multi-Factor Refresh Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor Refresh Token Max Age
**Corda:** MaxAgeMultiFactor

**Afeta:** Fichas de atualização

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de atualização para obter um novo par de token de acesso/atualização depois de terem sido autenticados com sucesso utilizando vários fatores. Depois de um utilizador autenticar e receber um novo token de atualização, o utilizador pode utilizar o fluxo de token de atualização durante o período de tempo especificado. (Isto é verdade desde que o token atual da atualização não seja revogado, e não seja desuso por mais tempo do que o tempo inativo.) Nessa altura, os utilizadores são obrigados a reauthenticar-se para receber um novo token de atualização.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você coloque esta propriedade num valor igual ou superior à propriedade Single-Factor Refresh Token Max Age.

### <a name="single-factor-session-token-max-age"></a>Sessão de fator único Token Max Age
**Corda:** MaxAgeSessionSingleFactor

**Afeta:** Fichas de sessão (persistentes e não permanentes)

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de sessão para obter um novo ID e token de sessão depois de terem sido autenticados com sucesso usando apenas um único fator. Depois de um utilizador autenticar e receber um novo token de sessão, o utilizador pode utilizar o fluxo de ficha de sessão durante o período de tempo especificado. (Isto é verdade desde que o token da sessão em curso não seja revogado e não tenha expirado.) Após o período de tempo especificado, o utilizador é obrigado a reauthenticar-se para receber um novo token de sessão.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade a um valor igual ou inferior à propriedade Multi-Factor Session Token Max Age.

### <a name="multi-factor-session-token-max-age"></a>Sessão multi-factor Token Max Age
**Corda:** MaxAgeSessionMultiFactor

**Afeta:** Fichas de sessão (persistentes e não permanentes)

**Resumo:** Esta política controla quanto tempo um utilizador pode usar um token de sessão para obter um novo ID e token de sessão após a última vez que autenticaram com sucesso usando vários fatores. Depois de um utilizador autenticar e receber um novo token de sessão, o utilizador pode utilizar o fluxo de ficha de sessão durante o período de tempo especificado. (Isto é verdade desde que o token da sessão em curso não seja revogado e não tenha expirado.) Após o período de tempo especificado, o utilizador é obrigado a reauthenticar-se para receber um novo token de sessão.

A redução da idade máxima obriga os utilizadores a autenticarem-se com mais frequência. Como a autenticação de um único fator é considerada menos segura do que a autenticação de vários fatores, recomendamos que você desemprete esta propriedade para um valor igual ou superior à propriedade Single-Factor Session Token Max Age.

## <a name="example-token-lifetime-policies"></a>Exemplo de políticas de vida simbólicas
Muitos cenários são possíveis no Azure AD quando você pode criar e gerir vidas simbólicas para apps, diretores de serviços e sua organização geral. Nesta secção, passamos por alguns cenários políticos comuns que podem ajudá-lo a impor novas regras para:

* Duração do Token
* Tempo Inativo Token Max
* Idade máxima token

Nos exemplos, pode aprender a:

* Gerir a política de incumprimento de uma organização
* Criar uma política para o sessão web
* Crie uma política para uma aplicação nativa que chame uma API web
* Gerir uma política avançada

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos seguintes, cria, atualiza, liga e elimina políticas para apps, diretores de serviços e organização em geral. Se você é novo no AZure AD, recomendamos que você aprenda sobre [como obter um inquilino AZure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.  

Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento do [Módulo Público Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Executar o `Connect` comando para iniciar seduca na sua conta de administração Azure. Executar este comando cada vez que começar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas que foram criadas na sua organização, executar o seguinte comando. Executar este comando após a maioria das operações nos seguintes cenários. Executar o comando também ajuda a obter o ** ** das suas políticas.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerir a política de incumprimento de uma organização
Neste exemplo, cria-se uma política que permite que os seus utilizadores assinem menos frequentemente em toda a sua organização. Para isso, crie uma política de vida simbólica para tokens single-factor refresh, que é aplicada em toda a sua organização. A política é aplicada a todas as aplicações da sua organização, e a cada diretor de serviço que ainda não tenha um conjunto de políticas.

1. Criar uma política de vida simbólica.

    1. Desacorra o Token de Atualização de Fator Único para "até revogar". O símbolo não expira até que o acesso seja revogado. Criar a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Para criar a apólice, executar o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para remover qualquer espaço em branco, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Para ver a sua nova política, e para obter o **ObjectId**da apólice, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atualize a apólice.

    Pode decidir que a primeira política que deu neste exemplo não é tão rigorosa como o seu serviço exige. Para definir o seu Token Single-Factor Refresh para expirar em dois dias, executar o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política para o sessão web

Neste exemplo, cria-se uma política que exige que os utilizadores autentem mais frequentemente na sua aplicação web. Esta política define a vida útil dos tokens de acesso/ID e a idade máxima de um token de sessão multi-factor para o principal de serviço da sua aplicação web.

1. Criar uma política de vida simbólica.

    Esta política, para o início de sessão web, define a vida útil do token de acesso/ID e a idade máxima de token de sessão de fator único para duas horas.

    1. Para criar a política, executar este comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, e para obter a política **ObjectId,** executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o **ObjectId** do seu principal de serviço.

    1. Utilize o [cmdlet Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todos os principais serviços da sua organização ou um único responsável de serviço.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Quando tiver o principal de serviço, executar o seguinte comando:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para uma aplicação nativa que chame uma API web
Neste exemplo, cria-se uma política que exige que os utilizadores autentem com menos frequência. A política também prolonga o tempo que um utilizador pode estar inativo antes de o utilizador ter de reautenticar. A política é aplicada à API web. Quando a aplicação nativa solicita a API web como um recurso, esta política é aplicada.

1. Criar uma política de vida simbólica.

    1. Para criar uma política rigorosa para uma API web, executar o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice à sua API web. Também precisa de obter o **ObjectId** da sua aplicação. Utilize o [cmdlet Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para encontrar o **ObjectId**da sua aplicação, ou utilize o [portal Azure](https://portal.azure.com/).

    Obtenha o **ObjectId** da sua aplicação e atribua a política:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerir uma política avançada
Neste exemplo, cria-se algumas políticas para aprender como funciona o sistema prioritário. Também aprende a gerir várias políticas que são aplicadas a vários objetos.

1. Criar uma política de vida simbólica.

    1. Para criar uma política padrão de organização que define a duração do Token single-factor para 30 dias, executar o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para ver a sua nova política, executar o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a apólice a um diretor de serviço.

    Agora, tem uma política que se aplica a toda a organização. Talvez queira preservar esta política de 30 dias para um responsável de serviços específico, mas mude a política de incumprimento da organização para o limite máximo de "até revogação".

    1. Para ver todos os principais serviços da sua organização, utilize o [cmdlet Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    1. Quando tiver o principal de serviço, executar o seguinte comando:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Coloque a `IsOrganizationDefault` bandeira em falso:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Criar uma nova política de incumprimento da organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Tem agora a política original ligada ao seu principal de serviço, e a nova política está definida como a política de incumprimento da sua organização. É importante lembrar que as políticas aplicadas aos principais serviços têm prioridade sobre as políticas de incumprimento da organização.

## <a name="cmdlet-reference"></a>Referência de cmdlets

### <a name="manage-policies"></a>Gerir políticas

Pode utilizar os seguintes cmdlets para gerir as políticas.

#### <a name="new-azureadpolicy"></a>Política new-AzureAD

Cria uma nova política.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Conjunto de JSON cordified que contém todas as regras da apólice. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Sequência do nome da apólice. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se for verdade, define a política como a política de incumprimento da organização. Se falso, não faz nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo de política. Para vidas simbólicas, use sempre "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcional] |Define uma identificação alternativa para a apólice. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as políticas AD da Azure ou uma política especificada.

```powershell
Get-AzureADPolicy
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code>[Opcional] |**ObjectId (ID)** da política que deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todas as aplicações e diretores de serviços que estão ligados a uma política.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Política set-AzureAD
Atualiza uma política existente.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que deseja. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Sequência do nome da apólice. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Opcional] |Conjunto de JSON cordified que contém todas as regras da apólice. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Opcional] |Se for verdade, define a política como a política de incumprimento da organização. Se falso, não faz nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Opcional] |Tipo de política. Para vidas simbólicas, use sempre "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcional] |Define uma identificação alternativa para a apólice. |`-AlternativeIdentifier "myAltId"` |

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
Pode utilizar os seguintes cmdlets para políticas de aplicação.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicação Política
Liga a política especificada a uma aplicação.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da apólice. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a apólice que é atribuída a uma aplicação.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remover-AzureADApplicaçãoPolição
Remove uma apólice de uma aplicação.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da apólice. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas principais de serviços
Pode utilizar os seguintes cmdlets para as políticas principais do serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Liga a política especificada a um diretor de serviço.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da apólice. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém qualquer política ligada ao principal de serviço especificado.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Remove a política do principal de serviço especificado.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da aplicação. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da apólice. | `-PolicyId <ObjectId of Policy>` |

## <a name="license-requirements"></a>Requisitos de licença

A utilização desta funcionalidade requer uma licença Azure AD Premium P1. Para encontrar a licença certa para os seus requisitos, consulte [Comparar funcionalidades geralmente disponíveis das edições Free and Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Os clientes com [licenças de negócios microsoft 365](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a funcionalidades de Acesso Condicional.
