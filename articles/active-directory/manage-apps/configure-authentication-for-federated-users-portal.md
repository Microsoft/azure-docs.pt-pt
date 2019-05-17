---
title: Configurar o início de sessão automático-acelerado para uma aplicação com uma política de deteção de Realm Inicial | Documentos da Microsoft
description: Saiba como configurar a política de deteção de Realm Inicial para a autenticação do Azure Active Directory para utilizadores federados, incluindo sugestões automático-acelerado e domínio.
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
ms.openlocfilehash: 18f7f6588cb4fb3b3b480402c3dad13be4a0ed2c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65781030"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurar o início de sessão do Azure Active Directory no comportamento de uma aplicação com uma política de deteção de Realm Inicial

Este artigo fornece uma introdução à configuração de comportamento de autenticação do Azure Active Directory para utilizadores federados. Ela abrange a configuração de autenticação e automático-acelerado restrições para os utilizadores nos domínios federados.

## <a name="home-realm-discovery"></a>Deteção de Território Doméstica
Deteção de Realm Inicial (HRD) é o processo que permite que o Azure Active Directory (Azure AD) para determinar em que um utilizador tem de autenticar em tempo de início de sessão.  Quando um utilizador inicia sessão a um inquilino do Azure AD para aceder a um recurso ou para a página de início de sessão comuns do Azure AD, eles escreva um nome de utilizador (UPN). O Azure AD utiliza para descobrir onde o utilizador tem de iniciar sessão. 

O utilizador poderá ter de ser direcionado para uma das seguintes localizações para ser autenticado:

- O principal de inquilino do utilizador (pode ser o mesmo inquilino, como o recurso que o utilizador está a tentar aceder). 

- Conta Microsoft.  O utilizador é um convidado no inquilino de recursos.

-  Um fornecedor de identidade no local, como serviços de Federação do Active Directory (AD FS).

- Outro fornecedor de identidade que esteja Federado com o inquilino do Azure AD.

## <a name="auto-acceleration"></a>Automático-acelerado 
Algumas organizações configurar domínios no seu inquilino do Azure Active Directory para federar com outro IdP, como o AD FS para autenticação de utilizador.  

Quando um utilizador inicia sessão numa aplicação, primeiro são apresentados com uma página de início de sessão do Azure AD. Depois de ter digitado respetivo UPN, se eles estão num domínio federado é direcionado para a página de início de sessão do IdP que serve esse domínio. Em determinadas circunstâncias, os administradores poderão querer direcionar os utilizadores para a página de início de sessão quando está a iniciar sessão para aplicações específicas. 

Como resultado, os utilizadores podem ignorar a página inicial do Azure Active Directory. Este processo é chamado de "início de sessão automático-acelerado."

Em casos em que o inquilino está federado para outro IdP para início de sessão, o automático-acelerado torna usuário início de sessão muito mais simplificados.  Pode configurar automático-acelerado de aplicativos individuais.

>[!NOTE]
>Se configurar uma aplicação para automático-acelerado, os utilizadores convidados não podem iniciar sessão. Se der um utilizador diretamente para um IdP federado para autenticação, não é possível para os mesmos voltar à página de início de sessão do Azure Active Directory. Os utilizadores convidados, que poderão ter de ser direcionado para outros inquilinos ou de um IdP externo, como uma conta Microsoft, não consigo iniciar sessão para essa aplicação porque está a ignorar o passo de deteção de Realm Inicial.  

Existem duas formas de controlar automático-acelerado para um IdP federado:   

- Utilize uma sugestão de domínio nos pedidos de autenticação para uma aplicação. 
- Configure uma política de deteção de Realm Inicial para ativar a aceleração de automática.

### <a name="domain-hints"></a>Sugestões de domínio    
Sugestões de domínio são diretivas que estão incluídas no pedido de autenticação a partir de uma aplicação. Eles podem ser usados para acelerar o utilizador para a federado IdP início de sessão na página. Ou eles podem ser usados por uma aplicação multi-inquilino para acelerar o utilizador diretamente para o com marca página início de sessão do Azure AD para o respetivo inquilino.  

Por exemplo, o aplicativo "largeapp.com" poderá permitir que os clientes aceder à aplicação num URL personalizado "contoso.largeapp.com." A aplicação também pode incluir uma sugestão de domínio contoso.com no pedido de autenticação. 

Sintaxe de sugestão de domínio varia consoante o protocolo que é utilizado e, normalmente, está configurado no aplicativo.

**WS-Federation**: whr=contoso.com na cadeia de consulta.

**SAML**:  De qualquer um autenticação pedido SAML que contenha uma sugestão de domínio ou um whr=contoso.com de cadeia de caracteres de consulta.

**Abrir ID Connect**: Um domain_hint=contoso.com de cadeia de caracteres de consulta. 

Se uma sugestão de domínio está incluída no pedido de autenticação da aplicação e o inquilino está Federado com esse domínio, do Azure AD tenta redirecionar o início de sessão para o IdP que está configurado para esse domínio. 

Se a sugestão de domínio não fazer referência a um domínio federado verificado, ela é ignorada e normal deteção de Realm inicial é invocada.

Para obter mais informações sobre automático-acelerado usando as sugestões de domínio que são suportadas pelo Azure Active Directory, consulte a [blogue Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Se uma sugestão de domínio está incluída numa solicitação de autenticação, sua presença substitui automático-acelerado que está definido para a aplicação na política HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Política de deteção de Realm Inicial para automático-acelerado
Alguns aplicativos não fornecem uma forma de configurar o pedido de autenticação que emitem. Nestes casos, não é possível utilizar sugestões de domínio para controlar automático-acelerado. Automático-acelerado pode ser configurado por meio da diretiva para alcançar o mesmo comportamento.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Ativar a autenticação direta para os aplicativos herdados
É melhor prática para aplicações para utilizar o início de sessão interativo e bibliotecas do AAD para autenticar os utilizadores. As bibliotecas de cuidam dos fluxos de utilizador federado.  Os aplicativos herdados, às vezes, não são escritos para entender o Federação. Eles não efetuam a deteção de realm inicial e não interagem com o ponto final federado correto para autenticar um usuário. Caso pretenda, pode utilizar a política de HRD para permitir que aplicativos herdados específicos que enviar credenciais de nome de utilizador/palavra-passe para autenticar diretamente com o Azure Active Directory. Sincronização de Hash de palavra-passe tem de estar ativada. 

> [!IMPORTANT]
> Ative a autenticação direta apenas se tiver ativado a sincronização de Hash de palavra-passe e sabe que há problema em autenticar nesta aplicação sem todas as políticas implementadas pelo seu IdP no local. Se desativar a sincronização de Hash de palavra-passe, ou desativar a sincronização de diretórios com o AD Connect por qualquer motivo, deve remover esta política para impedir a possibilidade de autenticação direta usando um hash de palavra-passe obsoletos.

## <a name="set-hrd-policy"></a>Definir política de HRD
Existem três passos à política HRD de definição num aplicativo para federado início de sessão automático-acelerado ou diretos aplicativos baseados na nuvem:

1. Crie uma política HRD.

2. Localize o principal de serviço ao qual pretende anexar a política.

3. Anexe a política ao principal de serviço. 

Políticas apenas em vigor para uma aplicação específica quando estão ligados a um principal de serviço. 

Apenas uma política HRD pode estar ativa num principal de serviço ao mesmo tempo.  

Pode utilizar o Microsoft Azure Active Directory Graph API diretamente ou o Azure Active Directory PowerShell cmdlets para criar e gerir a política HRD.

A Graph API que manipula a política está descrita com o [operações na política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artigo no MSDN.

Segue-se um exemplo de definição de política HRD:
    
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

O tipo de política é "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** é opcional. Se **AccelerateToFederatedDomain** é false, a política não tem efeito em automático-acelerado. Se **AccelerateToFederatedDomain** é true e há apenas um é verificado e domínio federado no inquilino, em seguida, os utilizadores será executado diretamente para o IdP federado para início de sessão. Se for true e não existe mais do que um domínio verificado do inquilino **PreferredDomain** tem de ser especificado.

**PreferredDomain** é opcional. **PreferredDomain** deve indicar um domínio ao qual pretende acelerar. Ele pode ser omitido se o inquilino tem apenas um domínio federado.  Se for omitida e há mais do que um domínio Federado de verificado, a política não tem efeito.

 Se **PreferredDomain** for especificado, terá de corresponder a um domínio verificado, federado para o inquilino. Todos os utilizadores da aplicação tem de ser capazes de iniciar sessão no domínio.

**AllowCloudPasswordValidation** é opcional. Se **AllowCloudPasswordValidation** é verdade, em seguida, o aplicativo tem permissão para autenticar um utilizador federado através da apresentação de credenciais de nome de utilizador/palavra-passe diretamente para o ponto de final de token do Azure Active Directory. Isso só funciona se a sincronização de Hash de palavra-passe estiver ativada.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação das políticas HRD
Políticas HRD podem ser criadas e, em seguida, atribuídas a organizações específicas e principais de serviço. Isso significa que é possível para múltiplas políticas aplicar a uma aplicação específica. A política HRD que entra em vigor segue estas regras:


- Se uma sugestão de domínio estiver presente no pedido de autenticação, em seguida, qualquer política HRD é ignorada para automático-acelerado. É utilizado o comportamento especificado pela sugestão de domínio.

- Caso contrário, se uma política está atribuída explicitamente ao principal de serviço, é imposto. 

- Se não há nenhuma sugestão de domínio, e nenhuma política está atribuída explicitamente ao principal de serviço, é aplicada uma política que está atribuída explicitamente para a organização de principal de serviço principal. 

- Se não há nenhuma sugestão de domínio e não a política foi atribuída para o principal de serviço ou a organização, é utilizado o comportamento HRD padrão.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial para a definição de política HRD num aplicativo 
Vamos utilizar cmdlets do Azure AD PowerShell para percorrer a alguns cenários, incluindo:


- Configuração da política de HRD fazer automático-acelerado para uma aplicação num inquilino com um único domínio federado.

- Configuração da política de HRD fazer automático-acelerado para uma aplicação a um dos vários domínios que são verificados para o seu inquilino.

- Configurar política HRD para ativar um aplicativo herdado para direcionar a autenticação de nome de utilizador/palavra-passe para o Azure Active Directory para um utilizador federado.

- Listagem dos aplicativos para o qual é configurada uma política.


### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, criar, atualizar, ligar e eliminar políticas nas principais de serviço de aplicações no Azure AD.

1.  Para começar, transfira a mais recente visualização de cmdlet do PowerShell do Azure AD. 

2.  Depois de transferir os cmdlets do Azure AD PowerShell, execute o comando de ligar para iniciar sessão no Azure AD com a sua conta de administrador:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o seguinte comando para ver todas as políticas da sua organização:

    ``` powershell
    Get-AzureADPolicy
    ```

Se não forem devolvidas informações, significa que não tem políticas criadas no seu inquilino.

### <a name="example-set-hrd-policy-for-an-application"></a>Exemplo: Definir a política de HRD para uma aplicação 

Neste exemplo, vai criar uma política que quando for atribuído a uma aplicação optar por: 
- Auto-acelera os utilizadores para um ecrã de início de sessão do AD FS quando eles estão a iniciar sessão a uma aplicação quando existe um único domínio no seu inquilino. 
- Utilizadores acelera de automática para um AD FS início de sessão tela é mais do que um domínio federado no seu inquilino.
- Permite que o nome de utilizador/palavra-passe não interativa de início de sessão diretamente no Azure Active Directory para utilizadores federados para as aplicações que a política é atribuída a.

#### <a name="step-1-create-an-hrd-policy"></a>Passo 1: Criar uma política HRD

A seguinte política auto-acelera os utilizadores para um ecrã de início de sessão do AD FS quando eles estão a iniciar sessão a uma aplicação quando existe um único domínio no seu inquilino.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A seguinte política automático-aceleram os utilizadores para um AD FS início de sessão ecrã há mais do que um domínio federado no seu inquilino. Se tiver mais de um domínio federado que autentique os utilizadores para aplicações, tem de especificar o domínio para acelerar o automático.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Para criar uma política para ativar a autenticação de nome de utilizador/palavra-passe para utilizadores federados diretamente com o Azure Active Directory para aplicações específicas, execute o seguinte comando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Para ver a nova política e ver as respetivas **ObjectID**, execute o seguinte comando:

``` powershell
Get-AzureADPolicy
```


Para aplicar a política HRD depois que a criou, pode atribuí-la para várias entidades de segurança de serviço de aplicações.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Passo 2: Localizar o principal de serviço ao qual pretende atribuir a política  
Terá do **ObjectID** os principais de serviço ao qual pretende atribuir a política. Existem várias formas de localizar os **ObjectID** de principais de serviço.    

Pode utilizar o portal ou pode consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Também pode ir para o [ferramenta do Explorador do gráfico](https://developer.microsoft.com/graph/graph-explorer) e inicie sessão na sua conta do Azure AD para ver os principais de serviço da sua organização. Uma vez que estiver a utilizar o PowerShell, pode utilizar o cmdlet get-AzureADServicePrincipal cmdlet para listar os principais de serviço e os respetivos IDs.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Passo 3: Atribuir a política para o seu principal de serviço  
Depois de ter o **ObjectID** do principal de serviço do aplicativo para o qual pretende configurar automático-acelerado, execute o seguinte comando. Este comando associa a política HRD que criou no passo 1 com o principal de serviço que localizou no passo 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Pode repetir este comando para cada principal de serviço ao qual pretende adicionar a política.

No caso em que uma aplicação já tem uma política de HomeRealmDiscovery atribuída, não poderá adicionar um segundo.  Nesse caso, altere a definição da política de deteção de Realm Inicial que é atribuída para o aplicativo para adicionar parâmetros adicionais.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Passo 4: Verifique os principais de serviço de aplicação a política HRD é atribuída a
Para verificar as aplicações que tenham a política HRD configurada, utilize o **Get-AzureADPolicyAppliedObject** cmdlet. Transmita-o **ObjectID** da política que pretende verificar.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Passo 5: Já está!
Experimente o aplicativo para verificar se a nova política está a funcionar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exemplo: Lista os aplicativos para o qual HRD política está configurada

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Passo 1: Listar todas as políticas que foram criadas na sua organização 

``` powershell
Get-AzureADPolicy
```

Tenha em atenção a **ObjectID** da política que pretende listar atribuições para.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Passo 2: Lista os principais de serviço ao qual a política é atribuída  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exemplo: Remover uma política HRD para uma aplicação
#### <a name="step-1-get-the-objectid"></a>Passo 1: Obtenha o ObjectID
Utilize o exemplo anterior para obter o **ObjectID** da política e que o principal de serviço de aplicativo do qual pretende removê-lo. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Passo 2: Remover a atribuição de política do principal de serviço de aplicações  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Passo 3: Verificar a remoção, listando os principais de serviço ao qual a política é atribuída 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre como funciona a autenticação no Azure AD, consulte [cenários de autenticação do Azure AD](../develop/authentication-scenarios.md).
- Para obter mais informações sobre o utilizador início de sessão único, consulte [acesso a aplicações e início de sessão único com o Azure Active Directory](configure-single-sign-on-portal.md).
- Visite o [Guia do programador do Active Directory](../develop/v1-overview.md) para uma descrição geral de todos os conteúdos relacionados ao desenvolvedor.
