---
title: Aplicativos de cliente público de conta única e múltipla Rio Azure
description: Uma visão geral de aplicações individuais e múltiplas de clientes públicos de conta.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80881355"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Aplicativos de cliente público de conta única e múltipla

Este artigo irá ajudá-lo a entender os tipos usados em aplicações de clientes públicos de conta única e múltipla, com foco em aplicações de clientes públicos de conta única. 

A Azure Ative Directory Authentication Library (ADAL) modela o servidor.  Em vez disso, a Microsoft Authentication Library (MSAL) modela a sua aplicação para clientes.  A maioria das aplicações Android são consideradas clientes públicos. Um cliente público é uma aplicação que não consegue manter um segredo.  

A MSAL especializa-se na superfície da API para simplificar e clarificar a experiência de `PublicClientApplication` desenvolvimento de apps que permitem a utilização de apenas uma conta de cada vez. `PublicClientApplication` é subclassificado por `SingleAccountPublicClientApplication` e `MultipleAccountPublicClientApplication` .  O diagrama que se segue mostra a relação entre estas classes.

![SingleAccountPublicCientApplication UML Class Diagrama](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Aplicação de cliente público de conta única

A `SingleAccountPublicClientApplication` classe permite-lhe criar uma aplicação baseada no MSAL que apenas permite que uma única conta seja assinada de cada vez. `SingleAccountPublicClientApplication` difere `PublicClientApplication` das seguintes formas:

- A MSAL rastreia a conta de assinatura.
  - Se a sua aplicação estiver a utilizar um corretor (o padrão durante o registo da aplicação do portal Azure) e estiver instalada num dispositivo onde um corretor esteja presente, a MSAL verificará se a conta ainda está disponível no dispositivo.
- `signIn` permite-lhe assinar uma conta explicitamente e separadamente de pedidos de âmbito.
- `acquireTokenSilent` não requer um parâmetro de conta.  Se fornecer uma conta, e a conta que fornece não corresponder à conta corrente rastreada pela MSAL, `MsalClientException` é lançada uma conta.
- `acquireToken` não permite que o utilizador troque de conta. Se o utilizador tentar mudar para uma conta diferente, é lançada uma exceção.
- `getCurrentAccount` devolve um objeto de resultado que fornece o seguinte:
  - Um booleano indicando se a conta mudou. Uma conta pode ser alterada em resultado de ser removida do dispositivo, por exemplo.
  - A conta anterior. Isto é útil se precisar de fazer qualquer limpeza de dados local quando a conta for removida do dispositivo ou quando uma nova conta é assinada.
  - O atual Acordo.
- `signOut` remove quaisquer fichas associadas ao seu cliente do dispositivo.  

Quando um corretor de autenticação Android, como o Microsoft Authenticator ou o Portal da Empresa Intune, estiver instalado no dispositivo e a sua aplicação estiver configurada para utilizar o corretor, `signOut` não removerá a conta do dispositivo.

## <a name="single-account-scenario"></a>Cenário de conta única

O seguinte pseudo-código ilustra a utilização `SingleAccountPublicClientApplication` .

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

## <a name="multiple-account-public-client-application"></a>Aplicação de cliente público de várias contas

A `MultipleAccountPublicClientApplication` classe é usada para criar aplicações baseadas no MSAL que permitem que várias contas sejam assinadas ao mesmo tempo. Permite-lhe obter, adicionar e remover contas da seguinte forma:

### <a name="add-an-account"></a>Adicionar uma conta

Utilize uma ou mais contas na sua aplicação ligando `acquireToken` uma ou mais vezes.  

### <a name="get-accounts"></a>Obter contas

- Ligue `getAccount` para obter uma conta específica.
- Ligue `getAccounts` para obter uma lista de contas atualmente conhecidas da app.

A sua aplicação não será capaz de enumerar todas as contas da plataforma de identidade da Microsoft no dispositivo conhecido pela aplicação do corretor. Só pode enumerar contas que tenham sido utilizadas pela sua app.  As contas que foram removidas do dispositivo não serão devolvidas por estas funções.

### <a name="remove-an-account"></a>Remover uma conta

Remova uma conta ligando `removeAccount` com um identificador de conta.

Se a sua aplicação estiver configurada para utilizar um corretor, e um corretor for instalado no dispositivo, a conta não será removida do corretor quando `removeAccount` ligar.  Apenas os tokens associados ao seu cliente são removidos.

## <a name="multiple-account-scenario"></a>Cenário de conta múltipla

O código pseudo-seguinte mostra como criar uma aplicação de conta múltipla, listar contas no dispositivo e adquirir fichas.

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
