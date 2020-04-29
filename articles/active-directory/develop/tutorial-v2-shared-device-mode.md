---
title: Utilização do modo de dispositivo partilhado com Android MSAL [ MSAL Android] Azure
description: Aprenda a preparar um dispositivo Android para funcionar em modo partilhado e executar uma aplicação de trabalho de primeira linha.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b2f74d2d441007f195abd38ca26ca7fa73605318
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886437"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Tutorial: Utilize o modo de dispositivo partilhado na sua aplicação Android

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="developer-guide"></a>Guia para programadores

Este guia fornece orientação para o desenvolvedor implementar o modo de dispositivo partilhado numa aplicação Android utilizando a Microsoft Authentication Library (MSAL). Consulte o [tutorial Android MSAL](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) para ver como integrar o MSAL com a sua aplicação Android, iniciar sessão num utilizador, ligar para o gráfico da Microsoft e assinar um utilizador.

### <a name="download-the-sample"></a>Transferir o exemplo

Clone a [aplicação](https://github.com/Azure-Samples/ms-identity-android-java/) da amostra do GitHub. A amostra tem a capacidade de trabalhar no [modo de conta única ou múltipla](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Adicione o SDK MSAL ao seu repositório maven local

Se não estiver a utilizar a aplicação de amostras, adicione a biblioteca MSAL como uma dependência no seu ficheiro build.gradle, assim:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Configure a sua aplicação para utilizar o modo de dispositivo partilhado

Consulte a documentação de [configuração](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) para obter mais informações sobre a configuração do seu ficheiro config.

`"shared_device_mode_supported"` Configurado `true` no seu ficheiro de configuração MSAL.

Pode não estar a planear suportar o modo de várias contas. Isto pode ser se não estiver a utilizar um dispositivo partilhado, e o utilizador pode iniciar sessão na aplicação com mais de uma conta ao mesmo tempo. Em caso afirmativo, definido `"account_mode"` para `"SINGLE"`. Isto garante que a `ISingleAccountPublicClientApplication`sua aplicação irá sempre obter , e simplifica significativamente a sua integração MSAL. O valor `"account_mode"` predefinido `"MULTIPLE"`é, por isso é importante alterar este valor no `"single account"` ficheiro config se estiver a utilizar o modo.

Aqui está um exemplo do ficheiro auth_config.json **app**>**main**>**res**>incluído no principal diretório**da** aplicação:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Detetar o modo de dispositivo partilhado

O modo de dispositivo partilhado permite configurar dispositivos Android para serem partilhados por vários colaboradores, ao mesmo tempo que fornece a gestão apoiada pela Microsoft Identity do dispositivo. Os colaboradores podem iniciar sessão nos seus dispositivos e aceder rapidamente à informação do cliente. Quando terminarem o seu turno ou tarefa, poderão assinar todas as aplicações no dispositivo partilhado com um único clique e o dispositivo estará imediatamente pronto para a utilização do próximo funcionário.

Utilize `isSharedDevice()` para determinar se uma aplicação está a funcionar num dispositivo que se encontra no modo de dispositivo partilhado. A sua aplicação pode utilizar esta bandeira para determinar se deve modificar o UX em conformidade.

Aqui está um corte de código que `isSharedDevice()`mostra como pode suster.  É da turma `SingleAccountModeFragment` da aplicação de amostras:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializar o objeto PublicClientApplication

Se colocar `"account_mode":"SINGLE"` no ficheiro config MSAL, pode lançar com segurança `ISingleAccountPublicCLientApplication`o objeto de aplicação devolvido como um .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Detetar modo de conta única vs. múltiplas

Se estiver a escrever uma aplicação que só será utilizada para trabalhadores de primeira linha num dispositivo partilhado, recomendamos que escreva a sua aplicação para suportar apenas o modo de conta única. Isto inclui a maioria das aplicações focadas em tarefas, tais como aplicações de registos médicos, aplicações de faturas e a maioria das aplicações de linha de negócio. Isto simplificará o seu desenvolvimento, uma vez que muitas funcionalidades do SDK não precisarão de ser acomodadas.

Se a sua aplicação suportar várias contas, bem como o modo de dispositivo partilhado, deve efetuar uma verificação de tipo e lançar para a interface apropriada, como mostrado abaixo.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obtenha o assinado no utilizador e determine se um utilizador mudou no dispositivo

O `loadAccount` método recupera a conta do utilizador assinado. O `onAccountChanged` método determina se o utilizador inscrito mudou e, em caso afirmativo, limpe:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Assine globalmente um utilizador

Os seguintes sinais num utilizador em todo o dispositivo para outras aplicações que utilizam o MSAL com a App Autenticador:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globalmente assinar um utilizador

O seguinte remove a conta de saque e limpa fichas em cache não só da app, mas também do dispositivo que se encontra no modo de dispositivo partilhado:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Guia do administrador

Os seguintes passos descrevem a instalação da sua aplicação no portal Azure e a colocação do seu dispositivo no modo de dispositivo partilhado.

### <a name="register-your-application-in-azure-active-directory"></a>Registe a sua candidatura no Diretório Ativo azure

Primeiro, registe a sua candidatura dentro do seu inquilino organizacional. Em seguida, forneça estes valores abaixo em auth_config.json para que a sua aplicação seja executada corretamente.

Para obter informações sobre como fazê-lo, consulte o [Registo da sua candidatura.](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application)

> [!NOTE]
> Quando registar a sua aplicação, utilize o guia quickstart no lado esquerdo e, em seguida, selecione **Android**. Isto irá levá-lo a uma página onde lhe será pedido que forneça o Nome do **Pacote** e **Signature Hash** para a sua aplicação. Estes são muito importantes para garantir que a configuração da sua aplicação funcione. Em seguida, receberá um objeto de configuração que pode usar para a sua aplicação que cortará e colará no seu ficheiro auth_config.json.

![Ecrã de](media/tutorial-v2-shared-device-mode/register-app.png) registo de aplicativos Deve selecionar **Faça esta alteração para mim** e, em seguida, fornecer os valores que o quickstart pede no portal Azure. Quando isso estiver feito, geraremos todos os ficheiros de configuração que precisar.

![Tela de informação de config app](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Configurar um inquilino

Para efeitos de teste, instale o seguinte no seu inquilino: pelo menos dois funcionários, um Administrador de Dispositivos Cloud e um Administrador Global. No portal Azure, detete o Administrador de Dispositivos cloud modificando funções organizacionais. No portal Azure, aceda às suas Funções Organizacionais selecionando funções de **diretório** > ativo azure**e administrador de dispositivos** > em**nuvem**. Adicione os utilizadores que podem colocar um dispositivo no modo partilhado.

## <a name="set-up-an-android-device-in-shared-mode"></a>Configurar um dispositivo Android em modo partilhado

### <a name="download-the-authenticator-app"></a>Descarregue a App Autenticador

Descarregue a Aplicação autenticadora da Microsoft a partir da loja Google Play. Se já tiver a app descarregada, certifique-se de que é a versão mais recente.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Definições de aplicativos autenticadores & registar o dispositivo na nuvem

Lance a App Autenticador e navegue para a página principal da conta. Assim que vir a página **Add Account,** está pronto para fazer o dispositivo partilhado.

![Autenticador adicionar ecrã de conta](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Vá ao painel **Definições** utilizando a barra de menus à direita. Selecione **Registo do Dispositivo** nas contas do Trabalho & **Escola**.

 ![Autenticador adicionar ecrã de conta](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Quando clicar neste botão, será-lhe pedido que autorize o acesso aos contactos do dispositivo. Isto deve-se à integração da conta do Android no dispositivo. Escolha **permitir**.

 ![Autenticador adicionar ecrã de conta](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

O Administrador de Dispositivos Cloud deve introduzir o seu e-mail organizacional em **Ou registar-se como um dispositivo partilhado**. Em seguida, clique no registo como botão **de dispositivo partilhado** e introduza as suas credenciais.

![ecrã de dispositivo de registo](media/tutorial-v2-shared-device-mode/register-device.png)

![sign-in](media/tutorial-v2-shared-device-mode/sign-in.png)

O dispositivo encontra-se agora em modo partilhado.

![ecrã de dispositivo de registo](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Quaisquer inscrições e inscrições no dispositivo serão globais, o que significa que se aplicam a todas as aplicações que estejam integradas com o MSAL e o Microsoft Authenticator no dispositivo. Agora pode implementar aplicações para o dispositivo que utilizam funcionalidades de modo de dispositivo partilhado.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Veja o dispositivo partilhado no portal Azure

Uma vez colocado um dispositivo em modo partilhado, torna-se conhecido da sua organização e é rastreado no seu inquilino organizacional. Pode ver os seus dispositivos partilhados olhando para o **Tipo De Adque** na lâmina de diretório Ativo Azure do seu portal Azure.

![Todas as lâminas de dispositivos no portal Azure](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Executar a aplicação de amostra

A Aplicação da Amostra é uma aplicação simples que vai chamar a API do gráfico da sua organização. Na primeira corrida será solicitado o consentimento, uma vez que o pedido é novo na sua conta de empregado.

![Tela de informação de config app](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o modo partilhado no [modo de dispositivo partilhado para dispositivos Android](msal-android-shared-devices.md)