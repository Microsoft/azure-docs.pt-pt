---
title: Configure a aceleração automática do sign-in usando home realm Discovery
description: Saiba como configurar a política de Descoberta do Home Realm para a autenticação do Diretório Ativo Azure para utilizadores federados, incluindo dicas de aceleração automática e de domínio.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfc964ffc394b3f79c9d279158003f383b7331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943432"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configure Azure Ative Diretório assina comportamento para uma aplicação usando uma política home realm discovery

Este artigo fornece uma introdução para configurar o comportamento de autenticação do Azure Ative Diretório para utilizadores federados. Abrange a configuração de restrições de aceleração automática e autenticação para os utilizadores em domínios federados.

## <a name="home-realm-discovery"></a>Deteção de Território Doméstica
Home Realm Discovery (HRD) é o processo que permite ao Azure Ative Directory (Azure AD) determinar onde um utilizador precisa de autenticar a tempo de início de hora de início.  Quando um utilizador inscreve num inquilino da AD Azure para aceder a um recurso, ou à página de entrada comum da AD Azure, escreve um nome de utilizador (UPN). A Azure AD usa-o para descobrir onde o utilizador precisa de iniciar sessão. 

O utilizador poderá ter de ser levado para um dos seguintes locais a autenticar:

- O inquilino da casa do utilizador (pode ser o mesmo inquilino que o recurso a que o utilizador está a tentar aceder). 

- Conta da Microsoft.  O utilizador é um hóspede no inquilino de recursos.

-  Um fornecedor de identidade no local, como os Serviços da Federação de Directórioactivo Ativo (AD FS).

- Outro fornecedor de identidade federado com o inquilino da AD Azure.

## <a name="auto-acceleration"></a>Aceleração automática 
Algumas organizações configuram domínios no seu inquilino do Azure Ative Directory para federar com outro IDP, como AD FS para autenticação do utilizador.  

Quando um utilizador assina numa aplicação, é apresentado pela primeira vez com uma página de entrada de AD Azure. Depois de terem dado a sua UPN, se estiverem num domínio federado, são levados para a página de inscrição do IDP que serve esse domínio. Em determinadas circunstâncias, os administradores podem querer direcionar os utilizadores para a página de sessão quando estão a iniciar sessão em aplicações específicas. 

Como resultado, os utilizadores podem saltar a página inicial do Diretório Ativo Do Azure. Este processo é referido como "aceleração automática de inscrição".

Nos casos em que o inquilino é federado para outro IDP para iniciar sessão, a aceleração automática torna o registo do utilizador mais simplificado.  Pode configurar a aceleração automática para aplicações individuais.

>[!NOTE]
>Se configurar uma aplicação para aceleração automática, os utilizadores convidados não podem iniciar o contrato. Se levar um utilizador diretamente a um IdP federado para autenticação, não há como voltar à página de inscrição do Diretório Ativo Azure. Os utilizadores convidados, que podem precisar de ser direcionados para outros inquilinos ou um IDP externo, como uma conta microsoft, não podem assinar essa aplicação porque estão a faltar ao passo Home Realm Discovery.  

Há duas maneiras de controlar a aceleração automática de um IDP federado:   

- Utilize uma dica de domínio sobre pedidos de autenticação para uma aplicação. 
- Configure uma política de Descoberta do Reino Doméstico para permitir a aceleração automática.

### <a name="domain-hints"></a>Dicas de domínio    
As sugestões de domínio são diretivas que estão incluídas no pedido de autenticação de um pedido. Podem ser utilizados para acelerar o utilizador à sua página de sinalização idp federada. Ou podem ser usados por uma aplicação multi-inquilino para acelerar o utilizador diretamente para a página de entrada da Marca Azure AD para o seu inquilino.  

Por exemplo, a aplicação "largeapp.com" pode permitir aos seus clientes aceder à aplicação num URL personalizado "contoso.largeapp.com". A aplicação também pode incluir uma dica de domínio para contoso.com no pedido de autenticação. 

A sintaxe de sugestão de domínio varia consoante o protocolo que é usado, e é tipicamente configurado na aplicação.

**WS-Federação**: whr=contoso.com na corda de consulta.

**SAML**: Ou um pedido de autenticação SAML que contém uma dica de domínio ou uma corda de consulta whr=contoso.com.

**Open ID Connect**: Uma cadeia de consulta domain_hint=contoso.com. 

Se uma sugestão de domínio for incluída no pedido de autenticação da aplicação, e o inquilino for federado com esse domínio, a Azure AD tenta redirecionar o início de sessão para o IDP que está configurado para esse domínio. 

Se a dica de domínio não se refere a um domínio federado verificado, é ignorado e a Descoberta normal do Reino doméstico é invocada.

Para obter mais informações sobre a aceleração automática utilizando as dicas de domínio que são suportadas pelo Azure Ative Directory, consulte o [blog Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se uma sugestão de domínio for incluída num pedido de autenticação, a sua presença substitui a aceleração automática definida para a aplicação na política de RHD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Política de Descoberta do Reino Doméstico para aceleração automática
Algumas aplicações não fornecem uma forma de configurar o pedido de autenticação que emitem. Nestes casos, não é possível usar sugestões de domínio para controlar a aceleração automática. A aceleração automática pode ser configurada através da política para alcançar o mesmo comportamento.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Ativar a autenticação direta para aplicações antigas
A melhor prática é que as aplicações utilizem bibliotecas AAD e entrada interativa para autenticar os utilizadores. As bibliotecas cuidam dos fluxos de utilizadores federados.  Às vezes, as aplicações antigas não são escritas para entender a federação. Não realizam a descoberta do reino doméstico e não interagem com o ponto final federado correto para autenticar um utilizador. Se optar, pode utilizar a Política de RHD para permitir aplicações específicas do legado que submetem credenciais de nome de utilizador/palavra-passe para autenticar diretamente com o Diretório Ativo Azure. A Password Hash Sync deve ser ativada. 

> [!IMPORTANT]
> Só ative a autenticação direta se tiver o Password Hash Sync ligado e sabe que não há problema em autenticar esta aplicação sem quaisquer políticas implementadas pelo seu IdP no local. Se desligar o Password Hash Sync ou desligar a Sincronização de Diretório com o AD Connect por qualquer motivo, deverá remover esta política para evitar a possibilidade de autenticação direta utilizando um hash de senha.

## <a name="set-hrd-policy"></a>Definir a política de HRD
Existem três etapas para definir a política de HRD sobre uma aplicação para aplicações federadas de aceleração automática ou baseadas em nuvem direta:

1. Criar uma política de RHD.

2. Localize o principal de serviço para anexar a apólice.

3. Anexar a apólice ao diretor de serviço. 

As políticas só têm efeito para uma aplicação específica quando estão anexadas a um diretor de serviço. 

Apenas uma política de RHD pode estar ativa num diretor de serviço a qualquer momento.  

Pode utilizar os cmdlets PowerShell do Diretório Ativo Azure para criar e gerir a política de RHD.

Segue-se um exemplo de definição de política de RHD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

O tipo de política é "HomeRealmDiscoveryPolicy".

**AccelerateToFederatedDomain** é opcional. Se **o AccelerateToFederatedDomain** for falso, a política não tem qualquer efeito na aceleração automática. Se **o AccelerateToFederatedDomain** for verdadeiro e houver apenas um domínio verificado e federado no inquilino, então os utilizadores serão levados diretamente para o IdP federado para iniciar sessão. Se for verdade e houver mais de um domínio verificado no inquilino, o **PreferredDomain** deve ser especificado.

**PreferredDomain** é opcional. **PreferredDomain** deve indicar um domínio para acelerar. Pode ser omitido se o inquilino tiver apenas um domínio federado.  Se for omitida, e houver mais de um domínio federado verificado, a política não tem qualquer efeito.

 Se o **PreferredDomain** for especificado, deve corresponder a um domínio verificado e federado para o inquilino. Todos os utilizadores da aplicação devem poder iniciar sessão nesse domínio.

**Permitir Validação de Passwords Do Utilizador** é opcional. Se o **AllowCloudPasswordValidação** for verdadeiro, a aplicação é autorizada a autenticar um utilizador federado apresentando credenciais de nome de utilizador/palavra-passe diretamente para o ponto final do Diretório Ativo Azure. Isto só funciona se o Password Hash Sync estiver ativado.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação das políticas de RHD
As políticas de RHD podem ser criadas e, em seguida, atribuídas a organizações específicas e diretores de serviços. Isto significa que é possível que várias políticas se apliquem a uma aplicação específica. A política de RHD que entra em vigor segue estas regras:


- Se uma sugestão de domínio estiver presente no pedido de autenticação, então qualquer política de HRD é ignorada para aceleração automática. O comportamento especificado pela sugestão de domínio é usado.

- Caso contrário, se uma política for explicitamente atribuída ao diretor de serviço, é aplicada. 

- Se não houver nenhuma pista de domínio, e nenhuma política for explicitamente atribuída ao diretor de serviço, uma política que é explicitamente atribuída à organização-mãe do diretor de serviço é aplicada. 

- Se não houver nenhuma pista de domínio, e nenhuma política foi atribuída ao diretor de serviço ou à organização, o comportamento padrão do HRD é usado.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial para definir a política de HRD numa aplicação 
Usaremos cmdlets Azure AD PowerShell para percorrer alguns cenários, incluindo:


- Criação de uma política de RHD para fazer aceleração automática para uma aplicação num inquilino com um único domínio federado.

- A criação da política de RHD para fazer aceleração automática para uma aplicação a um dos vários domínios que são verificados para o seu inquilino.

- Configurar a política de HRD para permitir que uma aplicação antiga faça a autenticação direta de username/password para o Diretório Ativo do Azure para um utilizador federado.

- Enumerando as aplicações para as quais uma política está configurada.


### <a name="prerequisites"></a>Pré-requisitos
Nos seguintes exemplos, cria, atualiza, liga e elimina políticas sobre os principais de serviços de aplicação em Azure AD.

1.  Para começar, baixe a mais recente pré-visualização de cmdlet Da AD AD. 

2.  Depois de ter descarregado os cmdlets Azure AD PowerShell, execute o comando Connect para iniciar sessão no Azure AD com a sua conta de administração:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o seguinte comando para ver todas as políticas da sua organização:

    ``` powershell
    Get-AzureADPolicy
    ```

Se nada for devolvido, significa que não tem nenhuma política criada no seu inquilino.

### <a name="example-set-hrd-policy-for-an-application"></a>Exemplo: Definir a política de HRD para uma aplicação 

Neste exemplo, cria-se uma política que, quando é atribuída a uma aplicação: 
- Acelera automaticamente os utilizadores para um ecrã de sinal de AD FS quando estão a iniciar sessão numa aplicação quando existe um único domínio no seu inquilino. 
- Automatiza automaticamente os utilizadores para um ecrã de sinal De AD FS há mais de um domínio federado no seu inquilino.
- Permite o sinal de username/palavra-passe não interativo diretamente para o Azure Ative Directory para utilizadores federados para as aplicações a que a política é atribuída.

#### <a name="step-1-create-an-hrd-policy"></a>Passo 1: Criar uma política de RHD

A seguinte política acelera automaticamente os utilizadores a um ecrã de sessão aD FS quando estão a iniciar sessão numa aplicação quando existe um único domínio no seu inquilino.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A seguinte política acelera automaticamente os utilizadores para um ecrã de sinal De AD FS há mais de um domínio federado no seu inquilino. Se tiver mais de um domínio federado que autentica os utilizadores para aplicações, precisa especificar o domínio para acelerar automaticamente.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Para criar uma política que permita a autenticação de username/password para utilizadores federados diretamente com o Diretório Ativo Azure para aplicações específicas, execute o seguinte comando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Para ver a sua nova política e obter o seu **ObjectID,** execute o seguinte comando:

``` powershell
Get-AzureADPolicy
```


Para aplicar a política de HRD depois de a ter criado, pode atribuí-la a vários diretores de serviços de aplicação.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Passo 2: Localizar o principal de serviço para atribuir a apólice  
Precisa do **ObjectID** dos diretores de serviço a que pretende atribuir a apólice. Existem várias formas de encontrar o **ObjectID** dos principais de serviço.    

Pode utilizar o portal, ou pode consultar o [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). Você também pode ir à [Ferramenta do Explorador de Gráficos](https://developer.microsoft.com/graph/graph-explorer) e iniciar sessão na sua conta Azure AD para ver todos os diretores de serviço da sua organização. 

Como está a utilizar o PowerShell, pode utilizar o seguinte cmdlet para listar os diretores de serviço e as suas identificações.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passo 3: Atribuir a apólice ao seu diretor de serviço  
Depois de ter o **ObjectID** do diretor de serviço da aplicação para o qual pretende configurar a aceleração automática, executar o seguinte comando. Este comando associa a política de HRD que criou no passo 1 com o diretor de serviço que localizou no passo 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Pode repetir este comando para cada diretor de serviço ao qual pretende adicionar a apólice.

No caso de uma aplicação já ter uma política HomeRealmDiscovery atribuída, não poderá adicionar uma segunda.  Nesse caso, altere a definição da política home realm discovery que é atribuída à aplicação para adicionar parâmetros adicionais.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Passo 4: Verifique quais os principais do serviço de aplicação a que a sua política de RHD é atribuída
Para verificar quais as aplicações configuradas pela política de HRD, utilize o cmdlet **Get-AzureADPolicyAppliedObject.** Passe-o o **ObjectID** da apólice que pretende verificar.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passo 5: Está feito!
Tente o pedido para verificar se a nova política está a funcionar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exemplo: Enumerar as aplicações para as quais a política de RHD está configurada

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Passo 1: Enumerar todas as políticas que foram criadas na sua organização 

``` powershell
Get-AzureADPolicy
```

Note o **ObjectID** da política para a quais pretende listar as atribuições.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Passo 2: Enumerar os diretores de serviço a que a política é atribuída  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exemplo: Remover uma política de RHD para uma aplicação
#### <a name="step-1-get-the-objectid"></a>Passo 1: Obtenha o ObjectID
Utilize o exemplo anterior para obter o **ObjectID** da apólice e o do diretor do serviço de aplicação a partir do qual pretende removê-lo. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passo 2: Retirar a atribuição de apólices do principal serviço de aplicação  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Passo 3: Verificar a remoção enumerando os diretores de serviço a que a apólice é atribuída 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como funciona a autenticação em Azure AD, consulte cenários de [autenticação para AD Azure](../develop/authentication-scenarios.md).
- Para obter mais informações sobre o utilizador, consulte o único sinal de [inscrição para aplicações no Diretório Ativo do Azure](what-is-single-sign-on.md).
- Visite a [plataforma de identidade](../develop/v2-overview.md) da Microsoft para uma visão geral de todos os conteúdos relacionados com o desenvolvedor.
