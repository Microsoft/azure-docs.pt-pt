---
title: Aplicativos de clientes públicos de conta única e múltiplas / Azure
description: Uma visão geral das aplicações de clientes públicos de conta única e múltipla.
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701421"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Aplicativos de clientes públicos de conta única e múltipla

Este artigo irá ajudá-lo a entender os tipos utilizados em aplicações de clientes públicos de conta única e múltipla, com foco em aplicações de clientes públicos de conta única. 

A Biblioteca de Autenticação de Diretório Ativo Azure (ADAL) modela o servidor.  A Microsoft Authentication Library (MSAL) em vez disso modela a sua aplicação cliente.  A maioria das aplicações Android são consideradas clientes públicos. Um cliente público é uma aplicação que não consegue guardar um segredo.  

A MSAL especializa a superfície da API de `PublicClientApplication` para simplificar e clarificar a experiência de desenvolvimento de apps que permitem apenas uma conta ser usada de cada vez. `PublicClientApplication` é subclassificada por `SingleAccountPublicClientApplication` e `MultipleAccountPublicClientApplication`.  O diagrama seguinte mostra a relação entre estas classes.

![Diagrama de classe UML de Publicação de Clientes Únicos](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Pedido de cliente público de conta única

A classe `SingleAccountPublicClientApplication` permite criar uma app baseada em MSAL que só permite que uma única conta seja assinada de cada vez. `SingleAccountPublicClientApplication` difere de `PublicClientApplication` das seguintes formas:

- A MSAL rastreia a conta atualmente assinada.
  - Se a sua aplicação estiver a utilizar um corretor (o predefinido durante o registo da aplicação do portal Azure) e estiver instalada num dispositivo onde um corretor está presente, a MSAL verificará se a conta ainda está disponível no dispositivo.
- `signIn` permite-lhe assinar uma conta de forma explícita e separada de solicitar âmbitos.
- `acquireTokenSilent` não requer um parâmetro de conta.  Se fornecer uma conta, e a conta que fornece não corresponder à conta corrente rastreada pela MSAL, é lançada uma `MsalClientException`.
- `acquireToken` não permite que o utilizador troque de contas. Se o utilizador tentar mudar para uma conta diferente, é lançada uma exceção.
- `getCurrentAccount` devolve um objeto de resultado que fornece o seguinte:
  - Um boolean o que indica se a conta mudou. Uma conta pode ser alterada como resultado de ser removida do dispositivo, por exemplo.
  - A conta anterior. Isto é útil se precisar de fazer qualquer limpeza de dados local quando a conta é removida do dispositivo ou quando uma nova conta é assinada.
  - A conta atual.
- `signOut` remove quaisquer fichas associadas ao seu cliente do dispositivo.  

Quando um corretor de autenticação Android, como o Microsoft Authenticator ou o Intune Company Portal, está instalado no dispositivo e a sua aplicação estiver configurada para utilizar o corretor, `signOut` não removerá a conta do dispositivo.

## <a name="single-account-scenario"></a>Cenário de conta única

O seguinte pseudo código ilustra a utilização `SingleAccountPublicClientApplication`.

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Pedido de cliente público de conta múltipla

A classe `MultipleAccountPublicClientApplication` é usada para criar aplicações baseadas em MSAL que permitem a assinatura de várias contas ao mesmo tempo. Permite-lhe obter, adicionar e remover contas da seguinte forma:

### <a name="add-an-account"></a>Adicionar uma conta

Utilize uma ou mais contas na sua aplicação, ligando para `acquireToken` uma ou mais vezes.  

### <a name="get-accounts"></a>Obter contas

- Ligue `getAccount` para obter uma conta específica.
- Ligue `getAccounts`para obter uma lista de contas atualmente conhecidas da app.

A sua aplicação não poderá enumerar todas as contas da plataforma de identidade da Microsoft no dispositivo conhecido da aplicação broker. Só pode enumerar contas que tenham sido utilizadas pela sua aplicação.  As contas que foram removidas do dispositivo não serão devolvidas por estas funções.

### <a name="remove-an-account"></a>Remover uma conta

Remova uma conta ligando para `removeAccount` com um identificador de conta.

Se a sua aplicação estiver configurada para utilizar um corretor e um corretor estiver instalado no dispositivo, a conta não será removida do corretor quando ligar para `removeAccount`.  Apenas fichas associadas ao seu cliente são removidas.

## <a name="multiple-account-scenario"></a>Cenário de conta múltipla

O seguinte código pseudo mostra como criar uma aplicação de conta múltipla, listar contas no dispositivo e adquirir fichas.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
