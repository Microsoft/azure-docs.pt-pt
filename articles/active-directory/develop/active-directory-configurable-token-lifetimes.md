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
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363976"
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

Os clientes usam fichas de acesso para aceder a um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de utilizador, cliente e recurso. As fichas de acesso não podem ser revogadas e são válidas até ao seu termo. Um ator malicioso que obteve um símbolo de acesso pode usá-lo para a extensão da sua vida. Ajustar a vida útil de um token de acesso é uma compensação entre melhorar o desempenho do sistema e aumentar o tempo que o cliente mantém o acesso após a desativação da conta do utilizador. O melhor desempenho do sistema é alcançado reduzindo o número de vezes que um cliente precisa para adquirir um novo token de acesso.  O padrão varia, dependendo da aplicação do cliente solicitando o token. Por exemplo, a avaliação contínua do acesso (CAE) a clientes capazes que negoceiam sessões conscientes do CAE verão uma vida útil útil (até 28 horas). Após o termo do token, o cliente deve usar o token de atualização para (geralmente silenciosamente) adquirir um novo token de atualização e ficha de acesso.

### <a name="saml-tokens"></a>Tokens SAML

Os tokens SAML são usados por muitas aplicações SaaS baseadas na web, e são obtidos usando o ponto final do protocolo SAML2 do Azure Ative Directory. Também são consumidos por aplicações que utilizam a WS-Federation. A vida útil padrão do símbolo é de 1 hora. Do ponto de vista da aplicação, o período de validade do token é especificado pelo valor notOnOrAfter do `<conditions …>` elemento no token. Após o fim do período de validade do token, o cliente deve iniciar um novo pedido de autenticação, que muitas vezes será satisfeito sem o sinal interativo como resultado do token de Sessão de Sinal Único (SSO).

O valor de NotOnOrAfter pode ser alterado usando o `AccessTokenLifetime` parâmetro num `TokenLifetimePolicy` . Será definido para a vida útil configurada na apólice, se houver, mais um fator de distorção do relógio de cinco minutos.

A confirmação do assunto NotOnOrAfter especificada no `<SubjectConfirmationData>` elemento não é afetada pela configuração Token Lifetime. 

### <a name="id-tokens"></a>Tokens de ID

As fichas de identificação são passadas para websites e clientes nativos. As fichas de identificação contêm informações de perfil sobre um utilizador. Um símbolo de identificação está ligado a uma combinação específica de utilizador e cliente. As fichas de identificação são consideradas válidas até ao seu termo. Normalmente, uma aplicação web corresponde à vida útil de um utilizador na aplicação ao tempo de vida útil do token de ID emitido para o utilizador. Pode ajustar o tempo de vida útil de um token de ID para controlar a frequência com que a aplicação web expira na sessão de aplicação, e com que frequência requer que o utilizador seja reautencido com a plataforma de identidade da Microsoft (silenciosamente ou interativamente).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Políticas de vida útil simbólicas para tokens de atualização e fichas de sessão

Não pode definir políticas vitalícias para tokens de atualização e fichas de sessão.

> [!IMPORTANT]
> A partir de 30 de janeiro de 2021 não poderá configurar a renovação e a sessão de token lifes. O Azure Ative Directory já não honra a configuração de atualização e símbolo de sessão nas políticas existentes.  Os novos tokens emitidos após a expiração dos tokens existentes estão agora definidos para a [configuração padrão](#configurable-token-lifetime-properties). Você ainda pode configurar o acesso, SAML, e ID token vidas após a renovação e sessão de configuração de configuração.
>
> A vida útil do símbolo existente não será alterada. Após o seu termo, um novo token será emitido com base no valor padrão.
>
> Se precisar de continuar a definir o período de tempo antes de um utilizador ser convidado a iniciar novamente o súmis, configurar a frequência de inscrição no Acesso Condicional. Para saber mais sobre Acesso Condicional, leia a gestão da [sessão de autenticação configurada com Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="configurable-token-lifetime-properties"></a>Propriedades de vida simbólicas configuradas
Uma política de vida simbólica é um tipo de objeto político que contém regras simbólicas de vida. Esta política controla quanto tempo o acesso, SAML e fichas de identificação para este recurso são considerados válidos. As políticas de vida útil simbólicas não podem ser definidas para tokens de atualização e sessão. Se não for definida nenhuma política, o sistema aplica o valor de vida predefinido.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Acesso, ID e PROPRIEDADES DE Política de Vida Simbólicas SAML2

A redução da propriedade Access Token Lifetime atenua o risco de um token de acesso ou símbolo de ID ser usado por um ator malicioso por um longo período de tempo. (Estas fichas não podem ser revogadas.) A compensação é que o desempenho é afetado negativamente, porque os tokens têm de ser substituídos com mais frequência.

Por exemplo, consulte [Criar uma política para iniciar sessão web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

A configuração de token de acesso, ID e SAML2 são afetadas pelas seguintes propriedades e pelos seus valores definidos respectivamente:

- **Propriedade**: Access Token Lifetime
- **Cadeia de propriedade política**: AccessTokenLifetime
- **Efeitos**: Tokens de acesso, fichas de ID, fichas SAML2
- **Predefinição:**
    - Fichas de acesso: varia, dependendo da aplicação do cliente que solicita o token. Por exemplo, a avaliação contínua do acesso (CAE) a clientes capazes que negoceiam sessões conscientes do CAE verão uma vida útil útil (até 28 horas).
    - Fichas de ID, fichas SAML2: 1 hora
- **Mínimo:** 10 minutos
- **Máximo**: 1 dia

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Atualizar e session token propriedades políticas de vida

A configuração de token de atualização e sessão é afetada pelas seguintes propriedades e pelos seus valores respectivamente definidos. Após a retirada da configuração de token de atualização e sessão em 30 de janeiro de 2021, a Azure AD apenas honrará os valores padrão descritos abaixo. Se decidir não utilizar [o Acesso Condicional](../conditional-access/howto-conditional-access-session-lifetime.md) para gerir a frequência de inscrição, as suas fichas de atualização e sessão serão definidas como configuração padrão nessa data e deixará de poder alterar as suas vidas.  

|Propriedade   |Cadeia de propriedade política    |Afeta |Predefinição |
|----------|-----------|------------|------------|
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
* Se nenhuma política tiver sido atribuída ao principal de serviço, à organização ou ao objeto de aplicação, os valores predefinidos são aplicados. (Consulte a tabela em [propriedades de vida de símbolo configurado](#configurable-token-lifetime-properties).)

Para obter mais informações sobre a relação entre objetos de aplicação e objetos principais de serviço, consulte [aplicação e objetos principais de serviço no Diretório Ativo Azure](app-objects-and-service-principals.md).

A validade de um símbolo é avaliada no momento em que o token é usado. A política com maior prioridade na aplicação que está a ser acedida entra em vigor.

Todos os intervalos de tempo utilizados aqui são formatados de acordo com o objeto [C# TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Então, 80 dias e 30 minutos `80.00:30:00` seriam.  O D líder pode ser derrubado se zero, então 90 minutos seriam `00:90:00` .  

## <a name="cmdlet-reference"></a>Referência de cmdlets

Estes são os cmdlets no [Azure Ative Directory PowerShell para o módulo de pré-visualização de gráficos](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals).

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

## <a name="next-steps"></a>Passos seguintes

Para saber mais, leia [exemplos de como configurar vidas simbólicas.](configure-token-lifetimes.md)
