---
title: Configurar a aceleração automática de entrada usando a descoberta de realm inicial
description: Saiba como configurar a política de descoberta de realm inicial para autenticação Azure Active Directory para usuários federados, incluindo dicas de domínio e aceleração automática.
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
ms.openlocfilehash: 174cdc31d7e5f29716febc7f68bbb410f33926c6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274633"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurar o comportamento de entrada Azure Active Directory para um aplicativo usando uma política de descoberta de realm inicial

Este artigo fornece uma introdução à configuração de Azure Active Directory comportamento de autenticação para usuários federados. Ele aborda a configuração de aceleração automática e restrições de autenticação para usuários em domínios federados.

## <a name="home-realm-discovery"></a>Deteção de Território Doméstica
O HRD (Home Realm Discovery) é o processo que permite ao Azure Active Directory (Azure AD) determinar onde um usuário precisa se autenticar no momento da entrada.  Quando um usuário entra em um locatário do Azure AD para acessar um recurso ou para a página de entrada comum do Azure AD, ele digita um nome de usuário (UPN). O Azure AD usa isso para descobrir onde o usuário precisa entrar. 

O usuário pode precisar ser levado para um dos seguintes locais a ser autenticado:

- O locatário inicial do usuário (pode ser o mesmo locatário do recurso que o usuário está tentando acessar). 

- conta Microsoft.  O usuário é um convidado no locatário do recurso.

-  Um provedor de identidade local, como o Serviços de Federação do Active Directory (AD FS) (AD FS).

- Outro provedor de identidade que é federado com o locatário do Azure AD.

## <a name="auto-acceleration"></a>Aceleração automática 
Algumas organizações configuram domínios em seu locatário Azure Active Directory para federar com outro IdP, como AD FS para autenticação de usuário.  

Quando um usuário entra em um aplicativo, ele é apresentado pela primeira vez com uma página de entrada do Azure AD. Depois de digitar seu UPN, se eles estiverem em um domínio federado, eles serão levados para a página de entrada do IdP servindo esse domínio. Em determinadas circunstâncias, os administradores podem querer direcionar os usuários para a página de entrada quando estiverem entrando em aplicativos específicos. 

Como resultado, os usuários podem ignorar a página de Azure Active Directory inicial. Esse processo é conhecido como "aceleração automática de entrada".

Nos casos em que o locatário é federado a outro IdP para entrada, a aceleração automática torna a entrada do usuário mais simplificada.  Você pode configurar a aceleração automática para aplicativos individuais.

>[!NOTE]
>Se você configurar um aplicativo para aceleração automática, os usuários convidados não poderão entrar. Se você levar um usuário diretamente para um IdP federado para autenticação, não haverá nenhuma maneira de fazê-los voltar à página de entrada Azure Active Directory. Os usuários convidados, que talvez precisem ser direcionados a outros locatários ou a um IdP externo, como um conta Microsoft, não podem entrar nesse aplicativo porque eles estão ignorando a etapa de descoberta de realm inicial.  

Há duas maneiras de controlar a aceleração automática para um IdP federado:   

- Use uma dica de domínio em solicitações de autenticação para um aplicativo. 
- Configure uma política de descoberta de realm inicial para habilitar a aceleração automática.

### <a name="domain-hints"></a>Dicas de domínio    
Dicas de domínio são diretivas que são incluídas na solicitação de autenticação de um aplicativo. Eles podem ser usados para acelerar o usuário para sua página de entrada IdP federada. Ou podem ser usados por um aplicativo multilocatário para acelerar o usuário diretamente para a página de entrada do Azure AD com marca para seu locatário.  

Por exemplo, o aplicativo "largeapp.com" pode permitir que seus clientes acessem o aplicativo em uma URL personalizada "contoso.largeapp.com". O aplicativo também pode incluir uma dica de domínio para contoso.com na solicitação de autenticação. 

A sintaxe de dica de domínio varia dependendo do protocolo que é usado e normalmente é configurada no aplicativo.

**WS-Federation**: WHR = contoso. com na cadeia de caracteres de consulta.

**SAML**: uma solicitação de autenticação SAML que contém uma dica de domínio ou uma cadeia de caracteres de consulta WHR = contoso. com.

**Open ID Connect**: uma cadeia de caracteres de consulta domain_hint = contoso. com. 

Se uma dica de domínio for incluída na solicitação de autenticação do aplicativo e o locatário for federado com esse domínio, o Azure AD tentará redirecionar a entrada para o IdP configurado para esse domínio. 

Se a dica de domínio não se referir a um domínio federado verificado, ela será ignorada e a descoberta de realm inicial normal será invocada.

Para obter mais informações sobre a aceleração automática usando as dicas de domínio com suporte no Azure Active Directory, consulte o [blog Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se uma dica de domínio for incluída em uma solicitação de autenticação, sua presença substituirá a aceleração automática definida para o aplicativo na política de HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Política de descoberta de realm inicial para aceleração automática
Alguns aplicativos não fornecem uma maneira de configurar a solicitação de autenticação que eles emitem. Nesses casos, não é possível usar dicas de domínio para controlar a aceleração automática. A aceleração automática pode ser configurada por meio da política para obter o mesmo comportamento.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Habilitar a autenticação direta para aplicativos herdados
A prática recomendada é que os aplicativos usem bibliotecas do AAD e logon interativo para autenticar usuários. As bibliotecas cuidam dos fluxos de usuário federados.  Às vezes, os aplicativos herdados não são escritos para entender a Federação. Eles não executam a descoberta de realm inicial e não interagem com o ponto de extremidade federado correto para autenticar um usuário. Se você optar por, poderá usar a política HRD para habilitar aplicativos herdados específicos que enviam credenciais de nome de usuário/senha para autenticar diretamente com Azure Active Directory. A sincronização de hash de senha deve ser habilitada. 

> [!IMPORTANT]
> Habilite somente a autenticação direta se você tiver a sincronização de hash de senha ativada e souber que não há problema em autenticar esse aplicativo sem nenhuma política implementada pelo IdP local. Se você desativar a sincronização de hash de senha ou desativar a sincronização de diretório com o AD Connect por qualquer motivo, deverá remover essa política para evitar a possibilidade de autenticação direta usando um hash de senha obsoleto.

## <a name="set-hrd-policy"></a>Definir política de HRD
Há três etapas para definir a política de HRD em um aplicativo para aceleração automática de entrada federada ou aplicativos baseados em nuvem diretos:

1. Crie uma política de HRD.

2. Localize a entidade de serviço à qual a política será anexada.

3. Anexe a política à entidade de serviço. 

As políticas só entram em vigor para um aplicativo específico quando são anexadas a uma entidade de serviço. 

Somente uma política de HRD pode estar ativa em uma entidade de serviço a qualquer momento.  

Você pode usar a API do Microsoft Azure Active Directory Graph diretamente ou os cmdlets do Azure Active Directory PowerShell para criar e gerenciar a política HRD.

O API do Graph que manipula a política é descrito no artigo [operações na política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) no msdn.

Veja a seguir um exemplo de definição de política HRD:
    
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

**AccelerateToFederatedDomain** é opcional. Se **AccelerateToFederatedDomain** for false, a política não terá efeito sobre a aceleração automática. Se **AccelerateToFederatedDomain** for true e houver apenas um domínio verificado e federado no locatário, os usuários serão levados diretamente para o IDP federado para entrar. Se for true e houver mais de um domínio verificado no locatário, **PreferredDomain** deverá ser especificado.

**PreferredDomain** é opcional. **PreferredDomain** deve indicar um domínio para o qual acelerar. Ele poderá ser omitido se o locatário tiver apenas um domínio federado.  Se ele for omitido e houver mais de um domínio federado verificado, a política não terá efeito.

 Se **PreferredDomain** for especificado, ele deverá corresponder a um domínio federado verificado para o locatário. Todos os usuários do aplicativo devem ser capazes de entrar nesse domínio.

**AllowCloudPasswordValidation** é opcional. Se **AllowCloudPasswordValidation** for true, o aplicativo poderá autenticar um usuário federado apresentando as credenciais de nome de usuário/senha diretamente para o ponto de extremidade do token de Azure Active Directory. Isso só funcionará se a sincronização de hash de senha estiver habilitada.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação de políticas de HRD
As políticas de HRD podem ser criadas e atribuídas a organizações específicas e entidades de serviço. Isso significa que é possível que várias políticas sejam aplicadas a um aplicativo específico. A política HRD que entra em vigor segue estas regras:


- Se uma dica de domínio estiver presente na solicitação de autenticação, qualquer política de HRD será ignorada para aceleração automática. O comportamento especificado pela dica de domínio é usado.

- Caso contrário, se uma política for explicitamente atribuída à entidade de serviço, ela será imposta. 

- Se não houver nenhuma dica de domínio e nenhuma política for explicitamente atribuída à entidade de serviço, uma política atribuída explicitamente à organização pai da entidade de serviço será imposta. 

- Se não houver nenhuma dica de domínio e nenhuma política tiver sido atribuída à entidade de serviço ou à organização, o comportamento padrão do HRD será usado.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial para definir a política HRD em um aplicativo 
Usaremos os cmdlets do PowerShell do Azure AD para percorrer alguns cenários, incluindo:


- Configurando a política HRD para fazer a aceleração automática para um aplicativo em um locatário com um único domínio federado.

- Configurar a política de HRD para fazer a aceleração automática de um aplicativo para um dos vários domínios que são verificados para seu locatário.

- Configurando a política HRD para permitir que um aplicativo herdado faça autenticação direta de nome de usuário/senha para Azure Active Directory para um usuário federado.

- Listando os aplicativos para os quais uma política está configurada.


### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas em entidades de serviço de aplicativo no Azure AD.

1.  Para começar, baixe a versão prévia do cmdlet do PowerShell do Azure AD mais recente. 

2.  Depois de baixar os cmdlets do PowerShell do Azure AD, execute o comando conectar para entrar no Azure AD com sua conta de administrador:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o seguinte comando para ver todas as políticas em sua organização:

    ``` powershell
    Get-AzureADPolicy
    ```

Se nada for retornado, isso significa que você não tem políticas criadas em seu locatário.

### <a name="example-set-hrd-policy-for-an-application"></a>Exemplo: definir a política de HRD para um aplicativo 

Neste exemplo, você cria uma política que, quando atribuída a um aplicativo, pode: 
- Acelera automaticamente os usuários para uma tela de entrada AD FS quando eles estão entrando em um aplicativo quando há um único domínio em seu locatário. 
- Acelera automaticamente os usuários para uma tela de entrada de AD FS há mais de um domínio federado em seu locatário.
- Habilita a entrada de nome de usuário/senha não interativa diretamente em Azure Active Directory para usuários federados para os aplicativos aos quais a política está atribuída.

#### <a name="step-1-create-an-hrd-policy"></a>Etapa 1: criar uma política de HRD

A política a seguir acelera automaticamente os usuários para uma tela de entrada AD FS quando eles estão entrando em um aplicativo quando há um único domínio em seu locatário.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A política a seguir acelera automaticamente os usuários para uma tela de entrada de AD FS há mais de um domínio federado em seu locatário. Se você tiver mais de um domínio federado que autentique usuários para aplicativos, será necessário especificar o domínio a ser acelerado automaticamente.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Para criar uma política para habilitar a autenticação de nome de usuário/senha para usuários federados diretamente com Azure Active Directory para aplicativos específicos, execute o seguinte comando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Para ver sua nova política e obter seu **ObjectID**, execute o seguinte comando:

``` powershell
Get-AzureADPolicy
```


Para aplicar a política HRD depois de criá-la, você pode atribuí-la a várias entidades de serviço do aplicativo.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Etapa 2: Localize a entidade de serviço à qual atribuir a política  
Você precisa do **ObjectID** das entidades de serviço às quais deseja atribuir a política. Há várias maneiras de localizar o **ObjectID** das entidades de serviço.    

Você pode usar o portal ou pode consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Você também pode ir para a [ferramenta Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) e entrar em sua conta do Azure ad para ver todas as entidades de serviço da sua organização. 

Como você está usando o PowerShell, você pode usar o cmdlet a seguir para listar as entidades de serviço e suas IDs.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Etapa 3: atribuir a política à entidade de serviço  
Depois de ter o **ObjectID** da entidade de serviço do aplicativo para o qual você deseja configurar a aceleração automática, execute o comando a seguir. Esse comando associa a política HRD que você criou na etapa 1 com a entidade de serviço que você localizou na etapa 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Você pode repetir esse comando para cada entidade de serviço à qual você deseja adicionar a política.

No caso em que um aplicativo já tem uma política HomeRealmDiscovery atribuída, você não poderá adicionar um segundo.  Nesse caso, altere a definição da política de descoberta de realm inicial atribuída ao aplicativo para adicionar parâmetros adicionais.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Etapa 4: verificar a quais entidades de serviço de aplicativo sua política de HRD está atribuída
Para verificar quais aplicativos têm a política HRD configurada, use o cmdlet **Get-AzureADPolicyAppliedObject** . Passe o **ObjectID** da política que você deseja verificar.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Etapa 5: você terminou!
Experimente o aplicativo para verificar se a nova política está funcionando.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exemplo: listar os aplicativos para os quais a política de HRD está configurada

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Etapa 1: listar todas as políticas que foram criadas em sua organização 

``` powershell
Get-AzureADPolicy
```

Observe o **ObjectID** da política para a qual você deseja listar as atribuições.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Etapa 2: listar as entidades de serviço às quais a política é atribuída  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exemplo: remover uma política de HRD para um aplicativo
#### <a name="step-1-get-the-objectid"></a>Etapa 1: obter o ObjectID
Use o exemplo anterior para obter a **ObjectID** da política e a entidade de serviço do aplicativo da qual você deseja removê-la. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Etapa 2: remover a atribuição de política da entidade de serviço do aplicativo  

``` powershell
Remove-AzureADApplicationPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Etapa 3: verificar a remoção listando as entidades de serviço às quais a política é atribuída 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como funciona a autenticação no Azure AD, consulte [cenários de autenticação do Azure ad](../develop/authentication-scenarios.md).
- Para obter mais informações sobre o logon único do usuário, consulte [logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md).
- Visite o [Guia do desenvolvedor Active Directory](../develop/v1-overview.md) para obter uma visão geral de todo o conteúdo relacionado ao desenvolvedor.
