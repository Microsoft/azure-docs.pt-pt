---
title: Tempos de vida de token configuráveis no Azure Active Directory | Microsoft Docs
description: Saiba como definir tempos de vida para tokens emitidos pelo Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: ryanwi
ms.custom: aaddev, annaba, identityplatformtop40
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9244dfabef8b13105ef830f9f4543da9cb2cca9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842645"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Tempos de vida de token configuráveis no Azure Active Directory (versão prévia)

Você pode especificar o tempo de vida de um token emitido pelo Azure Active Directory (Azure AD). Você pode definir tempos de vida de token para todos os aplicativos em sua organização, para um aplicativo multilocatário (várias organizações) ou para uma entidade de serviço específica em sua organização.

> [!IMPORTANT]
> Depois de ouvir os clientes durante a versão prévia, implementamos os [recursos de gerenciamento de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106) no acesso condicional do Azure AD. Você pode usar esse novo recurso para configurar tempos de vida de token de atualização definindo a frequência de entrada. Depois de 1º de novembro de 2019, você não poderá usar a política de tempo de vida de token configurável para configurar tokens de atualização, mas ainda poderá usá-lo para configurar tokens de acesso.

No Azure AD, um objeto de política representa um conjunto de regras que são impostas em aplicativos individuais ou em todos os aplicativos em uma organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que são aplicadas a objetos aos quais elas são atribuídas.

Você pode designar uma política como a política padrão para sua organização. A política é aplicada a qualquer aplicativo na organização, desde que ela não seja substituída por uma política com uma prioridade mais alta. Você também pode atribuir uma política a aplicativos específicos. A ordem de prioridade varia por tipo de política.

> [!NOTE]
> Não há suporte para a política de tempo de vida de token configurável para o SharePoint Online.  Embora você tenha a capacidade de criar essa política por meio do PowerShell, o SharePoint Online não reconhecerá essa política. Consulte o [blog do SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre como configurar tempos limite de sessão ociosa.
>* O tempo de vida padrão para o token de acesso do SharePoint Online é de 1 hora. 
>* O tempo máximo inativo padrão do token de atualização do SharePoint Online é de 90 dias.


## <a name="token-types"></a>Tipos de token

Você pode definir políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID.

### <a name="access-tokens"></a>Tokens de acesso

Os clientes usam tokens de acesso para acessar um recurso protegido. Um token de acesso pode ser usado somente para uma combinação específica de usuário, cliente e recurso. Tokens de acesso não podem ser revogados e são válidos até sua expiração. Um ator mal-intencionado que obteve um token de acesso pode usá-lo para a extensão do seu tempo de vida. Ajustar o tempo de vida de um token de acesso é uma compensação entre melhorar o desempenho do sistema e aumentar a quantidade de tempo que o cliente retém o acesso depois que a conta do usuário é desabilitada. O desempenho aprimorado do sistema é obtido com a redução do número de vezes que um cliente precisa adquirir um novo token de acesso.  O padrão é 1 hora-após 1 hora, o cliente deve usar o token de atualização para (normalmente silenciosamente) adquirir um novo token de atualização e token de acesso. 

### <a name="refresh-tokens"></a>Tokens de atualização

Quando um cliente adquire um token de acesso para acessar um recurso protegido, o cliente também recebe um token de atualização. O token de atualização é usado para obter novos pares de tokens de acesso/atualização quando o token de acesso atual expira. Um token de atualização está associado a uma combinação de usuário e cliente. Um token de atualização pode ser [revogado a qualquer momento](access-tokens.md#token-revocation)e a validade do token é verificada toda vez que o token é usado.  Os tokens de atualização não são revogados quando usados para buscar novos tokens de acesso – é recomendável, no entanto, excluir com segurança o token antigo ao obter um novo. 

É importante fazer uma distinção entre os clientes confidenciais e os clientes públicos, pois isso afeta por quanto tempo os tokens de atualização podem ser usados. Para obter mais informações sobre diferentes tipos de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente confidenciais
Clientes confidenciais são aplicativos que podem armazenar com segurança uma senha de cliente (segredo). Eles podem provar que as solicitações são provenientes do aplicativo cliente protegido e não de um ator mal-intencionado. Por exemplo, um aplicativo Web é um cliente confidencial, pois ele pode armazenar um segredo do cliente no servidor Web. Ele não é exposto. Como esses fluxos são mais seguros, os tempos de vida padrão de tokens de atualização emitidos `until-revoked`para esses fluxos são, não podem ser alterados usando a política e não serão revogados em redefinições de senha voluntárias.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente público

Os clientes públicos não podem armazenar com segurança uma senha de cliente (segredo). Por exemplo, um aplicativo iOS/Android não pode ofuscar um segredo do proprietário do recurso, portanto, é considerado um cliente público. Você pode definir políticas em recursos para impedir que tokens de atualização de clientes públicos com mais de um período especificado obtenham um novo par de tokens de acesso/atualização. (Para fazer isso, use a propriedade tempo máximo inativo do token de`MaxInactiveTime`atualização ().) Você também pode usar políticas para definir um período além do qual os tokens de atualização não são mais aceitos. (Para fazer isso, use a propriedade idade máxima do token de atualização.) Você pode ajustar o tempo de vida de um token de atualização para controlar quando e com que frequência o usuário precisa reinserir as credenciais, em vez de ser reautenticado silenciosamente, ao usar um aplicativo cliente público.

### <a name="id-tokens"></a>Tokens de ID
Tokens de ID são passados para sites e clientes nativos. Os tokens de ID contêm informações de perfil sobre um usuário. Um token de ID é associado a uma combinação específica de usuário e cliente. Os tokens de ID são considerados válidos até sua expiração. Normalmente, um aplicativo Web corresponde ao tempo de vida da sessão de um usuário no aplicativo para o tempo de vida do token de ID emitido para o usuário. Você pode ajustar o tempo de vida de um token de ID para controlar a frequência com que o aplicativo Web expira a sessão do aplicativo e com que frequência ele requer que o usuário seja reautenticado com o Azure AD (silenciosa ou interativamente).

### <a name="single-sign-on-session-tokens"></a>Tokens de sessão de logon único
Quando um usuário é autenticado com o Azure AD, uma sessão de logon único (SSO) é estabelecida com o navegador do usuário e o Azure AD. O token de SSO, na forma de um cookie, representa essa sessão. O token de sessão de SSO não está associado a um aplicativo cliente/recurso específico. Os tokens de sessão de SSO podem ser revogados e sua validade é verificada toda vez que eles são usados.

O Azure AD usa dois tipos de tokens de sessão de SSO: persistente e não persistente. Os tokens de sessão persistentes são armazenados como cookies persistentes pelo navegador. Os tokens de sessão não persistentes são armazenados como cookies de sessão. (Os cookies de sessão são destruídos quando o navegador é fechado.) Normalmente, um token de sessão não persistente é armazenado. Mas, quando o usuário seleciona a caixa de seleção **manter-me conectado** durante a autenticação, um token de sessão persistente é armazenado.

Os tokens de sessão não persistentes têm um tempo de vida de 24 horas. Os tokens persistentes têm um tempo de vida de 180 dias. Sempre que um token de sessão de SSO é usado dentro de seu período de validade, o período de validade é estendido em mais de 24 horas ou 180 dias, dependendo do tipo de token. Se um token de sessão de SSO não for usado em seu período de validade, ele será considerado expirado e não será mais aceito.

Você pode usar uma política para definir a hora em que o primeiro token de sessão foi emitido, além do qual o token de sessão não é mais aceito. (Para fazer isso, use a propriedade idade máxima de token de sessão.) Você pode ajustar o tempo de vida de um token de sessão para controlar quando e com que frequência um usuário precisa reinserir as credenciais, em vez de ser autenticado silenciosamente, ao usar um aplicativo Web.

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de tempo de vida do token
Uma política de tempo de vida de token é um tipo de objeto de política que contém regras de tempo de vida de token. Use as propriedades da política para controlar tempos de vida de token especificados. Se nenhuma política for definida, o sistema aplicará o valor de tempo de vida padrão.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de tempo de vida de token configuráveis
| Propriedade | Cadeia de caracteres da propriedade de política | Prejudica | Predefinição | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Tempo de vida do token de acesso |AccessTokenLifetime<sup>2</sup> |Tokens de acesso, tokens de ID, tokens de SAML2 |1 hora |10 minutos |1 dia |
| Tempo máximo inativo de token de atualização |MaxInactiveTime |Tokens de atualização |90 dias |10 minutos |90 dias |
| Idade máxima de token de atualização de fator único |MaxAgeSingleFactor |Tokens de atualização (para qualquer usuário) |Until-revogado |10 minutos |Until-revoked<sup>1</sup> |
| Idade máxima de token de atualização multifator |MaxAgeMultiFactor |Tokens de atualização (para qualquer usuário) |Until-revogado |10 minutos |Until-revoked<sup>1</sup> |
| Idade máxima de token de sessão de fator único |MaxAgeSessionSingleFactor |Tokens de sessão (persistentes e não persistentes) |Until-revogado |10 minutos |Until-revoked<sup>1</sup> |
| Idade máxima de token de sessão multifator |MaxAgeSessionMultiFactor |Tokens de sessão (persistentes e não persistentes) |Until-revogado |10 minutos |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento máximo explícito que pode ser definido para esses atributos.
* <sup>2</sup> Para que o cliente Web do Microsoft Teams funcione, é recomendável definir AccessTokenLifetime para mais de 15 minutos para as equipes da Microsoft.

### <a name="exceptions"></a>Exceções
| Propriedade | Prejudica | Predefinição |
| --- | --- | --- |
| Idade máxima do token de atualização (emitido para usuários federados que têm informações de revogação insuficientes<sup>1</sup>) |Tokens de atualização (emitidos para usuários federados que têm informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Tempo máximo inativo de token de atualização (emitido para clientes confidenciais) |Tokens de atualização (emitidos para clientes confidenciais) |90 dias |
| Idade máxima do token de atualização (emitido para clientes confidenciais) |Tokens de atualização (emitidos para clientes confidenciais) |Until-revogado |

* <sup>1</sup> Os usuários federados que têm informações de revogação insuficientes incluem todos os usuários que não têm o atributo "LastPasswordChangeTimestamp" sincronizado. Esses usuários recebem essa idade máxima curta porque o AAD não consegue verificar quando revogar tokens vinculados a uma credencial antiga (como uma senha que foi alterada) e deve verificar novamente com mais frequência para garantir que o usuário e os tokens associados ainda estejam em boas condições  permanente. Para melhorar essa experiência, os administradores de locatários devem garantir que estejam sincronizando o atributo "LastPasswordChangeTimestamp" (isso pode ser definido no objeto de usuário usando o PowerShell ou por meio de AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Avaliação e priorização de política
Você pode criar e atribuir uma política de tempo de vida de token a um aplicativo específico, à sua organização e às entidades de serviço. Várias políticas podem se aplicar a um aplicativo específico. A política de tempo de vida do token que entra em vigor segue estas regras:

* Se uma política for explicitamente atribuída à entidade de serviço, ela será imposta.
* Se nenhuma política for explicitamente atribuída à entidade de serviço, uma política explicitamente atribuída à organização pai da entidade de serviço será imposta.
* Se nenhuma política for explicitamente atribuída à entidade de serviço ou à organização, a política atribuída ao aplicativo será imposta.
* Se nenhuma política tiver sido atribuída à entidade de serviço, à organização ou ao objeto de aplicativo, os valores padrão serão impostos. (Consulte a tabela em [Propriedades de tempo de vida de token configuráveis](#configurable-token-lifetime-properties).)

Para obter mais informações sobre a relação entre objetos de aplicativo e objetos de entidade de serviço, consulte objetos de entidade de serviço [e de aplicativo no Azure Active Directory](app-objects-and-service-principals.md).

A validade de um token é avaliada no momento em que o token é usado. A política com a prioridade mais alta no aplicativo que está sendo acessado entra em vigor.

Todos os TimeSpans usados aqui são formatados de C# acordo com o objeto [TimeSpan](/dotnet/api/system.timespan) -D. hh: mm: SS.  Por isso, 80 dias e 30 minutos `80.00:30:00`seriam.  O D à esquerda pode ser Descartado se for zero, então 90 minutos `00:90:00`seria.  

> [!NOTE]
> Aqui está um cenário de exemplo.
>
> Um usuário deseja acessar dois aplicativos Web: Aplicativo Web A e aplicativo Web B.
> 
> Fatores
> * Ambos os aplicativos Web estão na mesma organização pai.
> * A política de tempo de vida de token 1 com uma idade máxima de token de sessão de oito horas é definida como o padrão da organização pai.
> * O aplicativo Web A é um aplicativo Web de uso regular e não está vinculado a nenhuma política.
> * O aplicativo Web B é usado para processos altamente confidenciais. Sua entidade de serviço está vinculada à política de tempo de vida de token 2, que tem uma idade máxima de token de sessão de 30 minutos.
>
> Às 12:00 PM, o usuário inicia uma nova sessão do navegador e tenta acessar o aplicativo Web A. O usuário é redirecionado para o Azure AD e é solicitado a entrar. Isso cria um cookie que tem um token de sessão no navegador. O usuário é Redirecionado de volta para o aplicativo Web A com um token de ID que permite ao usuário acessar o aplicativo.
>
> Às 12:15 PM, o usuário tenta acessar o aplicativo Web B. O navegador redireciona para o Azure AD, que detecta o cookie de sessão. A entidade de serviço do aplicativo Web B está vinculada à política de tempo de vida do token 2, mas também faz parte da organização pai, com a política de tempo de vida do token padrão 1. A política de tempo de vida de token 2 entra em vigor porque as políticas vinculadas a entidades de serviço têm uma prioridade mais alta do que as políticas padrão da organização. O token de sessão foi originalmente emitido nos últimos 30 minutos, portanto, é considerado válido. O usuário é Redirecionado de volta para o aplicativo Web B com um token de ID que concede acesso a eles.
>
> Às 1:00 PM, o usuário tenta acessar o aplicativo Web A. O usuário é redirecionado para o Azure AD. O aplicativo Web A não está vinculado a nenhuma política, mas como está em uma organização com a política de tempo de vida de token padrão 1, essa política entra em vigor. O cookie de sessão que foi originalmente emitido nas últimas oito horas é detectado. O usuário é redirecionado silenciosamente de volta para o aplicativo Web A com um novo token de ID. Não é necessário autenticar o usuário.
>
> Imediatamente depois, o usuário tenta acessar o aplicativo Web B. O usuário é redirecionado para o Azure AD. Como antes, a política de tempo de vida do token 2 entra em vigor. Como o token foi emitido há mais de 30 minutos, é solicitado que o usuário insira novamente suas credenciais de entrada. Um token de sessão totalmente novo e um token de ID são emitidos. O usuário pode acessar o aplicativo Web B.
>
>

## <a name="configurable-policy-property-details"></a>Detalhes da propriedade de política configurável
### <a name="access-token-lifetime"></a>Tempo de vida do token de acesso
**Strings** AccessTokenLifetime

**Prejudica** Tokens de acesso, tokens de ID

**Resumo** Esta política controla por quanto tempo os tokens de acesso e de ID para esse recurso são considerados válidos. A redução da propriedade de tempo de vida do token de acesso reduz o risco de um token de acesso ou token de ID ser usado por um ator mal-intencionado por um longo período de tempo. (Esses tokens não podem ser revogados.) A desvantagem é que o desempenho é afetado negativamente, pois os tokens precisam ser substituídos com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Tempo máximo inativo de token de atualização
**Strings** MaxInactiveTime

**Prejudica** Tokens de atualização

**Resumo** Essa política controla o quão antigo um token de atualização pode ser antes de um cliente não poder mais usá-lo para recuperar um novo par de tokens de acesso/atualização ao tentar acessar esse recurso. Como um novo token de atualização geralmente é retornado quando um token de atualização é usado, essa política impede o acesso se o cliente tentar acessar qualquer recurso usando o token de atualização atual durante o período de tempo especificado.

Essa política força os usuários que não estão ativos em seu cliente a autenticar novamente para recuperar um novo token de atualização.

A propriedade tempo máximo inativo do token de atualização deve ser definida com um valor mais baixo do que a idade máxima do token de fator único e as propriedades de idade máxima do token de atualização multifator.

### <a name="single-factor-refresh-token-max-age"></a>Idade máxima de token de atualização de fator único
**Strings** MaxAgeSingleFactor

**Prejudica** Tokens de atualização

**Resumo** Essa política controla por quanto tempo um usuário pode usar um token de atualização para obter um novo par de tokens de acesso/atualização após a última autenticação bem-sucedida usando apenas um único fator. Depois que um usuário é autenticado e recebe um novo token de atualização, o usuário pode usar o fluxo do token de atualização para o período de tempo especificado. (Isso é verdadeiro, desde que o token de atualização atual não seja revogado e não seja deixado não utilizado por mais tempo do que a hora inativa.) Nesse ponto, o usuário é forçado a autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que você defina essa propriedade com um valor igual ou menor que a propriedade idade máxima do token de atualização multifator.

### <a name="multi-factor-refresh-token-max-age"></a>Idade máxima de token de atualização multifator
**Strings** MaxAgeMultiFactor

**Prejudica** Tokens de atualização

**Resumo** Essa política controla por quanto tempo um usuário pode usar um token de atualização para obter um novo par de tokens de acesso/atualização após a última autenticação bem-sucedida usando vários fatores. Depois que um usuário é autenticado e recebe um novo token de atualização, o usuário pode usar o fluxo do token de atualização para o período de tempo especificado. (Isso é verdadeiro, desde que o token de atualização atual não seja revogado e não seja usado por mais tempo do que a hora inativa.) Nesse ponto, os usuários são forçados a autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que você defina essa propriedade com um valor igual ou maior que a propriedade idade máxima do token de atualização de fator único.

### <a name="single-factor-session-token-max-age"></a>Idade máxima de token de sessão de fator único
**Strings** MaxAgeSessionSingleFactor

**Prejudica** Tokens de sessão (persistentes e não persistentes)

**Resumo** Essa política controla por quanto tempo um usuário pode usar um token de sessão para obter uma nova ID e um novo token de sessão após a última autenticação bem-sucedida usando apenas um único fator. Depois que um usuário é autenticado e recebe um novo token de sessão, o usuário pode usar o fluxo de token de sessão para o período de tempo especificado. (Isso será verdadeiro, desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período de tempo especificado, o usuário é forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que você defina essa propriedade com um valor igual ou menor que a propriedade idade máxima de token de sessão multifator.

### <a name="multi-factor-session-token-max-age"></a>Idade máxima de token de sessão multifator
**Strings** MaxAgeSessionMultiFactor

**Prejudica** Tokens de sessão (persistentes e não persistentes)

**Resumo** Essa política controla por quanto tempo um usuário pode usar um token de sessão para obter uma nova ID e um novo token de sessão após a última vez que eles foram autenticados com êxito usando vários fatores. Depois que um usuário é autenticado e recebe um novo token de sessão, o usuário pode usar o fluxo de token de sessão para o período de tempo especificado. (Isso será verdadeiro, desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período de tempo especificado, o usuário é forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos que você defina essa propriedade com um valor igual ou maior que a propriedade idade máxima de token de sessão de fator único.

## <a name="example-token-lifetime-policies"></a>Exemplo de políticas de tempo de vida de token
Muitos cenários são possíveis no Azure AD quando você pode criar e gerenciar tempos de vida de token para aplicativos, entidades de serviço e sua organização em geral. Nesta seção, examinaremos alguns cenários de política comuns que podem ajudá-lo a impor novas regras para:

* Duração do Token
* Tempo máximo inativo do token
* Idade máxima do token

Nos exemplos, você pode aprender a:

* Gerenciar a política padrão de uma organização
* Criar uma política para entrada na Web
* Criar uma política para um aplicativo nativo que chama uma API da Web
* Gerenciar uma política avançada

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas para aplicativos, entidades de serviço e sua organização em geral. Se você for novo no Azure AD, recomendamos que saiba [como obter um locatário do Azure ad](quickstart-create-new-tenant.md) antes de prosseguir com esses exemplos.  

Para começar, execute as seguintes etapas:

1. Baixe a versão mais recente da [Visualização pública do módulo do Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
2. Execute o `Connect` comando para entrar em sua conta de administrador do Azure AD. Execute esse comando toda vez que iniciar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas que foram criadas em sua organização, execute o comando a seguir. Execute este comando após a maioria das operações nos cenários a seguir. A execução do comando também ajuda você a obter o * * * * de suas políticas.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerenciar a política padrão de uma organização
Neste exemplo, você cria uma política que permite que os usuários façam logon com menos frequência em toda a organização. Para fazer isso, crie uma política de tempo de vida de token para tokens de atualização de fator único, que é aplicada em sua organização. A política é aplicada a todos os aplicativos em sua organização e a cada entidade de serviço que ainda não tem uma política definida.

1. Criar uma política de tempo de vida do token.

    1. Defina o token de atualização de fator único para "até a revogação". O token não expira até que o acesso seja revogado. Crie a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. Para criar a política, execute o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Para ver sua nova política e obter o **ObjectID**da política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atualize a política.

    Você pode decidir que a primeira política definida neste exemplo não é tão estrita quanto o seu serviço requer. Para definir o token de atualização de fator único para expirar em dois dias, execute o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política para entrada na Web

Neste exemplo, você cria uma política que exige que os usuários se autentiquem com mais frequência em seu aplicativo Web. Essa política define o tempo de vida dos tokens de acesso/ID e a idade máxima de um token de sessão multifator para a entidade de serviço do seu aplicativo Web.

1. Criar uma política de tempo de vida do token.

    Essa política, para entrada na Web, define o tempo de vida do token de acesso/ID e a idade máxima de token de sessão de fator único para duas horas.

    1. Para criar a política, execute este comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Para ver sua nova política e obter o **ObjectID**da política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atribua a política à entidade de serviço. Você também precisa obter o **ObjectID** da entidade de serviço.

    1. Use o cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todas as entidades de serviço de sua organização ou uma única entidade de serviço.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Quando você tiver a entidade de serviço, execute o seguinte comando:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para um aplicativo nativo que chama uma API da Web
Neste exemplo, você cria uma política que exige que os usuários se autentiquem com menos frequência. A política também aumenta a quantidade de tempo que um usuário pode ficar inativo antes que o usuário precise se autenticar novamente. A política é aplicada à API da Web. Quando o aplicativo nativo solicita a API da Web como um recurso, essa política é aplicada.

1. Criar uma política de tempo de vida do token.

    1. Para criar uma política estrita para uma API da Web, execute o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Para ver sua nova política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atribua a política à sua API da Web. Você também precisa obter o **ObjectID** do seu aplicativo. Use o cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para localizar o **ObjectID**do seu aplicativo ou use o [portal do Azure](https://portal.azure.com/).

    Obtenha o **ObjectID** do seu aplicativo e atribua a política:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerenciar uma política avançada
Neste exemplo, você cria algumas políticas para saber como o sistema de prioridade funciona. Você também aprenderá como gerenciar várias políticas que são aplicadas a vários objetos.

1. Criar uma política de tempo de vida do token.

    1. Para criar uma política padrão da organização que defina o tempo de vida do token de atualização de fator único para 30 dias, execute o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Para ver sua nova política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atribua a política a uma entidade de serviço.

    Agora, você tem uma política que se aplica a toda a organização. Talvez você queira preservar essa política de 30 dias para uma entidade de serviço específica, mas alterar a política padrão da organização para o limite superior de "até a revogação".

    1. Para ver todas as entidades de serviço de sua organização, use o cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    2. Quando você tiver a entidade de serviço, execute o seguinte comando:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. Defina o `IsOrganizationDefault` sinalizador como false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Criar uma nova política padrão da organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Agora você tem a política original vinculada à sua entidade de serviço e a nova política é definida como a política padrão da sua organização. É importante lembrar que as políticas aplicadas às entidades de serviço têm prioridade sobre as políticas padrão da organização.

## <a name="cmdlet-reference"></a>Referência de cmdlets

### <a name="manage-policies"></a>Gerenciar políticas

Você pode usar os cmdlets a seguir para gerenciar políticas.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Cria uma nova política.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matriz de JSON em cadeias que contém todas as regras da política. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Cadeia de caracteres do nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se for true, definirá a política como a política padrão da organização. Se for false, não fará nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Tipo de política. Para tempos de vida de token, sempre use "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Adicional |Define uma ID alternativa para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as políticas do Azure AD ou uma política especificada.

```powershell
Get-AzureADPolicy
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code>Adicional |**ObjectId (ID)** da política que você deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todos os aplicativos e entidades de serviço que estão vinculados a uma política.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que você deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Atualiza uma política existente.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que você deseja. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Cadeia de caracteres do nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>Adicional |Matriz de JSON em cadeias que contém todas as regras da política. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>Adicional |Se for true, definirá a política como a política padrão da organização. Se for false, não fará nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>Adicional |Tipo de política. Para tempos de vida de token, sempre use "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Adicional |Define uma ID alternativa para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Exclui a política especificada.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que você deseja. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Políticas de aplicativo
Você pode usar os cmdlets a seguir para políticas de aplicativo.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Vincula a política especificada a um aplicativo.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectID** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a política atribuída a um aplicativo.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Remove uma política de um aplicativo.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectID** da política. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas de principal de serviço
Você pode usar os cmdlets a seguir para as políticas de entidade de serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Vincula a política especificada a uma entidade de serviço.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectID** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém qualquer política vinculada à entidade de serviço especificada.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Remove a política da entidade de serviço especificada.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectID** da política. | `-PolicyId <ObjectId of Policy>` |
