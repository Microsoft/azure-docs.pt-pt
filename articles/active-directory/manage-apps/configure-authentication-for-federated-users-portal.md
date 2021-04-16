---
title: Configurar a aceleração automática de assinatura usando Home Realm Discovery
description: Saiba como configurar a política home realm Discovery para a autenticação do Azure Ative Directory para utilizadores federados, incluindo dicas de aceleração automática e de domínio.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92dea75855ab1e5486b39d072692e72b26c4da1c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377773"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurar o Azure Ative Directory assinar em comportamento para uma aplicação usando uma política de Descoberta do Realm Doméstico

Este artigo fornece uma introdução ao comportamento de autenticação do Azure Ative Directory para utilizadores federados usando a política Home Realm Discovery (HRD).  Cobre a utilização de uma aceleração automática para saltar o ecrã de entrada do nome de utilizador e encaminhar automaticamente os utilizadores para pontos finais de login federados.  A Microsoft não recomenda configurar a aceleração automática por mais tempo, uma vez que pode impedir a utilização de métodos de autenticação mais fortes, como o FIDO e dificulta a colaboração.

## <a name="home-realm-discovery"></a>Deteção de Território Doméstica

Home Realm Discovery (HRD) é o processo que permite ao Azure Ative Directory (Azure AD) determinar com que fornecedor de identidade ("IdP") um utilizador precisa de autenticar no momento da insinuação.  Quando um utilizador assina um inquilino AZure AD para aceder a um recurso, ou à página de inscrição comum AZure AD, digita um nome de utilizador (UPN). A Azure AD usa isso para descobrir onde o utilizador precisa de fazer o seu sposição.

O utilizador será levado a um dos seguintes fornecedores de identidade a autenticar:

- O inquilino da casa do utilizador (pode ser o mesmo inquilino que o recurso a que o utilizador está a tentar aceder).

- Conta Microsoft.  O utilizador é um hóspede no inquilino de recursos que utiliza uma conta de consumo para a autenticação.

- Um fornecedor de identidade no local, como o Ative Directory Federation Services (AD FS).

- Outro fornecedor de identidade que é federado com o inquilino da AD Azure.

## <a name="auto-acceleration"></a>Aceleração automática

Algumas organizações configuram domínios no seu inquilino Azure Ative Directory para federar com outro IdP, como AD FS para a autenticação do utilizador.  

Quando um utilizador assina uma aplicação, é apresentado pela primeira vez com uma página de entrada AD Azure. Depois de terem dactilografado a sua UPN, se estiverem num domínio federado, são levados para a página de inscrição do IdP que serve esse domínio. Em certas circunstâncias, os administradores podem querer direcionar os utilizadores para a página de entrada quando estão a iniciar sessão em aplicações específicas.

Como resultado, os utilizadores podem saltar a página inicial do Azure Ative Directory. Este processo é referido como "iniciar a aceleração automática".

Nos casos em que o inquilino é federado para outro IdP para a inscrição, a aceleração automática torna o registo do utilizador mais simplificado.  Pode configurar a aceleração automática para aplicações individuais.

>[!NOTE]
>Se configurar uma aplicação para a aceleração automática, os utilizadores não podem usar credenciais geridas (como o FIDO) e os utilizadores convidados não podem iniciar sação. Se levar um utilizador diretamente para um IdP federado para autenticação, não há forma de eles voltarem à página de entrada do Azure Ative Directory. Os utilizadores convidados, que podem precisar de ser direcionados para outros inquilinos ou um IdP externo, como uma conta microsoft, não podem entrar nessa aplicação porque estão a saltar o passo home realm Discovery.  

Existem três formas de controlar a auto-aceleração para um IdP federado:

- Utilize uma [dica de domínio](#domain-hints) sobre pedidos de autenticação para uma aplicação.
- Configure uma política de Descoberta do Reino Doméstico para forçar a [auto-aceleração](#home-realm-discovery-policy-for-auto-acceleration).
- Configure uma política de Descoberta do Home Realm para [ignorar sugestões](prevent-domain-hints-with-home-realm-discovery.md) de domínio de aplicações específicas ou para determinados domínios.

### <a name="domain-hints"></a>Dicas de domínio

As dicas de domínio são diretivas que estão incluídas no pedido de autenticação de uma aplicação. Podem ser usados para acelerar o utilizador para a sua página de inscrição federada do IdP. Ou podem ser usados por uma aplicação multi-arrendatário para acelerar o utilizador diretamente para a página de inscrição AZure AD marcada para o seu inquilino.  

Por exemplo, a aplicação "largeapp.com" pode permitir aos seus clientes aceder à aplicação num URL personalizado "contoso.largeapp.com". A aplicação também pode incluir uma dica de domínio para contoso.com no pedido de autenticação.

A sintaxe de sugestão de domínio varia dependendo do protocolo que é usado, e é tipicamente configurado na aplicação.

**WS-Federação**: whr=contoso.com na cadeia de consulta.

**SAML**: Ou um pedido de autenticação SAML que contenha uma sugestão de domínio ou uma sequência de consulta whr=contoso.com.

**Ligue id aberta**: Uma cadeia de consulta domain_hint=contoso.com.

Por padrão, a Azure AD tenta redirecionar o sed-in para o IdP configurado para um domínio se **ambos forem** verdadeiros:

- Uma dica de domínio está incluída no pedido de autenticação da aplicação **e**
- O inquilino é federado com esse domínio.

Se a sugestão de domínio não se refere a um domínio federado verificado, é ignorado.

Para obter mais informações sobre a aceleração automática utilizando as dicas de domínio que são suportadas pelo Azure Ative Directory, consulte o [blog Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se uma sugestão de domínio for incluída num pedido de autenticação e [deve ser respeitada,](#home-realm-discovery-policy-to-prevent-auto-acceleration)a sua presença substitui a aceleração automática definida para a aplicação na política de RHD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Política de Descoberta do Home Realm para a aceleração automática

Algumas aplicações não fornecem uma forma de configurar o pedido de autenticação que emitem. Nestes casos, não é possível usar dicas de domínio para controlar a aceleração automática. A aceleração automática pode ser configurada através da política home realm Discovery para alcançar o mesmo comportamento.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Política de Descoberta do Home Realm para evitar a aceleração automática

Algumas aplicações da Microsoft e do SaaS incluem automaticamente domain_hints (por exemplo, `https://outlook.com/contoso.com` resulta num pedido de login com `&domain_hint=contoso.com` apêndice), o que pode perturbar o lançamento de credenciais geridas como o FIDO.  Pode utilizar a [Home Realm Discovery Policy](/graph/api/resources/homeRealmDiscoveryPolicy) para ignorar sugestões de domínio de determinadas aplicações ou para determinados domínios, durante o lançamento de credenciais geridas.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Ativar a autenticação direta ropc de utilizadores federados para aplicações antigas

A melhor prática é que as aplicações utilizem bibliotecas AAD e insuportem interativas para autenticar os utilizadores. As bibliotecas cuidam dos fluxos de utilizador federados.  Por vezes, aplicações antigas, especialmente aquelas que usam subvenções ROPC, submetem o nome de utilizador e a palavra-passe diretamente à AZure AD, e não são escritas para entender a federação. Não realizam a descoberta do reino doméstico e não interagem com o ponto final federado correto para autenticar um utilizador. Se optar por isso, pode utilizar a Política de HRD para permitir aplicações antigas específicas que submetam credenciais de nome de utilizador/palavra-passe utilizando a concessão ROPC para autenticar diretamente com o Azure Ative Directory. O Hash Sync da palavra-passe tem de ser ativado.

> [!IMPORTANT]
> Só ativar a autenticação direta se tiver o Password Hash Sync ligado e sabe que não há problema em autenticar esta aplicação sem quaisquer políticas implementadas pelo IdP no local. Se desligar o Password Hash Sync ou desligar a Sincronização do Diretório com AD Connect por qualquer motivo, deve remover esta política para evitar a possibilidade de autenticação direta utilizando um haxixe de senha.

## <a name="set-hrd-policy"></a>Definir a política de HRD

Existem três etapas para definir a política de HRD numa aplicação para aplicações federadas de aceleração automática ou baseadas em nuvem direta:

1. Criar uma política de HRD.

2. Localize o principal de serviço ao qual se anexe a apólice.

3. Anexe a apólice ao diretor de serviço.

As políticas só têm efeito para uma aplicação específica quando estão ligadas a um principiante de serviço.

Apenas uma política de HRD pode ser ativa num diretor de serviço a qualquer momento.  

Pode utilizar os cmdlets PowerShell do Diretório Ativo Azure para criar e gerir a política de HRD.

Segue-se um exemplo de definição de política de RHD:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

O tipo de política é "[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)".

**AccelerateToFederatedDomain** é opcional. Se **accelerateToFederatedDomain** é falso, a política não tem qualquer efeito na aceleração automática. Se **accelerateToFederatedDomain** for verdadeiro e houver apenas um domínio verificado e federado no inquilino, então os utilizadores serão levados diretamente para o IdP federado para iniciar sação. Se for verdade e houver mais de um domínio verificado no arrendatário, **a PreferredDomain** deve ser especificada.

**PreferencialMenteDomain** é opcional. **A PreferredDomain** deve indicar um domínio para acelerar. Pode ser omitido se o inquilino tiver apenas um domínio federado.  Se for omitida, e houver mais de um domínio federado verificado, a política não tem efeito.

 Se **a PreferredDomain** for especificada, deve corresponder a um domínio verificado e federado para o inquilino. Todos os utilizadores da aplicação devem poder iniciar sôms nesse domínio - os utilizadores que não puderem entrar no domínio federado ficarão presos e incapazes de fazer o sinal.

**Permitir a continuação doCloudPassword é** opcional. Se **o AllowCloudPasswordValidation** for verdadeiro, então a aplicação é permitida a autenticação de um utilizador federado apresentando credenciais de nome de utilizador/palavra-passe diretamente no ponto final do Azure Ative Directory. Isto só funciona se o Password Hash Sync estiver ativado.

Além disso, existem duas opções de RHD ao nível do inquilino, não mostradas acima:

- **AlternateIdLogin** é opcional.  Se estiver ativado, isto [permite que os utilizadores assinem com os seus endereços de e-mail em vez da SUA UPN](../authentication/howto-authentication-use-email-signin.md) no sinal AZure AD na página.  Os IDs alternativos dependem do utilizador não ser automaticamente acelerado para um IDP federado.

- **DomainHintPolicy** é um objeto complexo opcional que [ *impede* dicas de domínio de utilizadores que aceleram automaticamente para domínios federados](prevent-domain-hints-with-home-realm-discovery.md). Esta configuração em todo o inquilino é usada para garantir que as aplicações que enviam dicas de domínio não impeçam os utilizadores de iniciar sessão com credenciais geridas pela nuvem.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação das políticas de RHD

As políticas de HRD podem ser criadas e, em seguida, atribuídas a organizações específicas e diretores de serviços. Isto significa que é possível que várias políticas se apliquem a uma aplicação específica, pelo que a Azure AD deve decidir qual delas tem precedência. Um conjunto de regras decide qual a política de RHD (de muitos aplicados) produz efeitos:

- Se uma dica de domínio estiver presente no pedido de autenticação, então a política de HRD para o inquilino (a política definida como o inpadrão do inquilino) é verificada para ver se [as dicas de domínio devem ser ignoradas](prevent-domain-hints-with-home-realm-discovery.md). Se forem permitidas indicações de domínio, o comportamento especificado pela sugestão de domínio é usado.

- Caso contrário, se uma apólice for explicitamente atribuída ao diretor de serviço, é aplicada.

- Se não houver nenhuma sugestão de domínio, e nenhuma política for explicitamente atribuída ao diretor de serviço, uma política que seja explicitamente atribuída à organização-mãe do principal de serviço é aplicada.

- Se não houver nenhuma sugestão de domínio, e nenhuma política tiver sido atribuída ao principal de serviço ou à organização, o comportamento padrão de HRD é usado.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial para definir a política de HRD em uma aplicação

Usaremos cmdlets Azure AD PowerShell para percorrer alguns cenários, incluindo:

- Criação de uma política de HRD para fazer a aceleração automática para uma aplicação num inquilino com um único domínio federado.

- Configurar a política de HRD para fazer a aceleração automática para uma aplicação a um dos vários domínios que são verificados para o seu inquilino.

- Configurar a política de HRD para permitir que uma aplicação legado faça a autenticação direta do nome de utilizador/palavra-passe para o Azure Ative Directory para um utilizador federado.

- Enumeração das candidaturas para as quais uma apólice está configurada.

### <a name="prerequisites"></a>Pré-requisitos

Nos exemplos seguintes, cria, atualiza, liga e elimina políticas sobre os principais dos serviços de aplicações em Azure AD.

1. Para começar, descarregue a mais recente pré-visualização do cmdlet Azure AD PowerShell.

2. Depois de ter descarregado os cmdlets Azure AD PowerShell, execute o comando Connect para iniciar sação no Azure AD com a sua conta de administração:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Executar o seguinte comando para ver todas as políticas da sua organização:

    ``` powershell
    Get-AzureADPolicy
    ```

Se nada for devolvido, significa que não tem políticas criadas no seu inquilino.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Exemplo: Definir uma política de HRD para uma aplicação

Neste exemplo, cria-se uma política que, quando é atribuída a uma aplicação:

- Acelera automaticamente os utilizadores para um ecrã de sessão de sessão AD FS quando estão a iniciar sessão numa aplicação quando há um único domínio no seu inquilino.
- Acelera automaticamente os utilizadores para um ecrã de sind-in AD FS há mais de um domínio federado no seu inquilino.
- Permite o nome de utilizador/palavra-passe não interativo entrar diretamente no Azure Ative Directory para utilizadores federados para as aplicações a que a política está atribuída.

#### <a name="step-1-create-an-hrd-policy"></a>Passo 1: Criar uma política de HRD

A seguinte política acelera automaticamente os utilizadores para um ecrã de sessão de sessão AD FS quando estão a iniciar sessão numa aplicação quando há um único domínio no seu inquilino.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

A seguinte política acelera automaticamente os utilizadores para um ecrã de sind-in AD FS há mais de um domínio federado no seu inquilino. Se tiver mais de um domínio federado que autentica os utilizadores para aplicações, precisa de especificar o domínio para acelerar automaticamente.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Para criar uma política que permita a autenticação de nome de utilizador/palavra-passe para utilizadores federados diretamente com o Azure Ative Directory para aplicações específicas, executar o seguinte comando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Para ver a sua nova política e obter o seu **ObjectID,** executar o seguinte comando:

``` powershell
Get-AzureADPolicy
```

Para aplicar a política de HRD depois de a ter criado, pode atribuí-la a vários princípios de serviço de aplicação.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Passo 2: Localizar o principal de serviço a que atribuir a apólice

Precisa do **ObjectID** dos principais de serviço aos quais pretende atribuir a apólice. Existem várias formas de encontrar o **ObjectID** dos principais serviços.

Pode utilizar o portal ou consultar o [Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta). Também pode ir à [Ferramenta Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) e iniciar sôm no seu AD Azure para ver todos os principais serviços da sua organização.

Como está a utilizar o PowerShell, pode utilizar o cmdlet seguinte para listar os principais de serviço e os seus IDs.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passo 3: Atribuir a apólice ao seu diretor de serviço

Depois de ter o **ObjectID** do principal de serviço da aplicação para a qual pretende configurar a aceleração automática, executar o seguinte comando. Este comando associa a política de HRD que criou no passo 1 com o principal de serviço que localizou no passo 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Pode repetir este comando para cada diretor de serviço ao qual pretende adicionar a apólice.

No caso de uma aplicação já ter uma política homeRealmDiscovery atribuída, não poderá adicionar uma segunda.  Nesse caso, altere a definição da política Home Realm Discovery que é atribuída à aplicação para adicionar parâmetros adicionais.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Passo 4: Verifique quais os principais do serviço de aplicação a que a sua política de RHD está atribuída

Para verificar quais aplicações configuraram a política de HRD, utilize o **cmdlet Get-AzureADPolicyAppliedObject.** Passe-lhe o **ObjectID** da política que pretende verificar.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Passo 5: Acabou-se!

Tente o pedido para verificar se a nova política está a funcionar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exemplo: Listar as aplicações para as quais a política de HRD está configurada

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Passo 1: Listar todas as políticas que foram criadas na sua organização

``` powershell
Get-AzureADPolicy
```

Note o **ObjectID** da política para a política para a que pretende listar atribuições.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Passo 2: Listar os princípios de serviço aos quais a apólice é atribuída  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Exemplo: Remover uma política de HRD de uma aplicação

#### <a name="step-1-get-the-objectid"></a>Passo 1: Obter o ObjectID

Utilize o exemplo anterior para obter o **ObjectID** da apólice e o do principal do serviço de aplicações a partir do qual pretende removê-lo.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passo 2: Remover a atribuição de política do diretor do serviço de aplicações  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Passo 3: Verificar a remoção listando os principais de serviço a que a apólice é atribuída

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como funciona a autenticação em Azure AD, consulte [cenários de autenticação para Azure AD](../develop/authentication-vs-authorization.md).
- Para obter mais informações sobre o único súmis do utilizador, consulte [um único sinal de inscrição para aplicações no Azure Ative Directory](what-is-single-sign-on.md).
- Visite a [plataforma de identidade](../develop/v2-overview.md) da Microsoft para obter uma visão geral de todos os conteúdos relacionados com o programador.