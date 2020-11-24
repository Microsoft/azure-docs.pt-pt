---
title: 'Tutorial: Utilize o modo de dispositivo partilhado com a Microsoft Authentication Library (MSAL) para Android Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, aprende-se a preparar um dispositivo Android para funcionar em modo partilhado e executar uma aplicação de trabalhador de primeira linha.
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
ms.openlocfilehash: 981d3a0c5d01d70625fc0d022318c5bc866f23a0
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95756405"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Tutorial: Utilize o modo de dispositivo partilhado na sua aplicação Android

Neste tutorial, os desenvolvedores Android e Azure Ative Directory (Azure AD) os administradores de inquilinos aprendem sobre o código, app Authenticator e configurações de inquilinos necessários para ativar o modo de dispositivo partilhado para uma aplicação Android.

Neste tutorial:

> [!div class="checklist"]
> * Faça o download de uma amostra de código
> * Ativar e detetar o modo dispositivo partilhado
> * Detetar o modo de conta simples ou múltipla
> * Detetar um interruptor de utilizador e ativar o sign-in global e a assinatura
> * Configurar inquilino e registar a candidatura no portal Azure
> * Configurar um dispositivo Android em modo dispositivo partilhado
> * Execute a aplicação de exemplo

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="developer-guide"></a>Guia para programadores

Esta secção do tutorial fornece orientação do desenvolvedor para implementar o modo de dispositivo partilhado numa aplicação Android utilizando a Microsoft Authentication Library (MSAL). Consulte o tutorial do [MSAL Android](./tutorial-v2-android.md) para ver como integrar o MSAL com a sua aplicação Android, iniciar sposição num utilizador, ligar para o gráfico da Microsoft e assinar um utilizador.

### <a name="download-the-sample"></a>Transferir o exemplo

Clone a [aplicação](https://github.com/Azure-Samples/ms-identity-android-java/) da amostra do GitHub. A amostra tem a capacidade de funcionar no [modo de conta única ou multi- conta](./single-multi-account.md).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Adicione o MSAL SDK ao seu repositório local de Maven

Se não estiver a utilizar a aplicação de amostras, adicione a biblioteca MSAL como uma dependência no seu ficheiro build.gradle, assim:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Configure a sua app para utilizar o modo de dispositivo partilhado

Consulte a documentação de [configuração](./msal-configuration.md) para obter mais informações sobre a configuração do seu ficheiro config.

Configurar `"shared_device_mode_supported"` para o seu ficheiro de `true` configuração MSAL.

Pode não estar a planear suportar o modo de conta múltipla. Pode ser se não estiver a utilizar um dispositivo partilhado, e o utilizador puder entrar na aplicação com mais do que uma conta ao mesmo tempo. Em caso afirmativo, desação `"account_mode"` para `"SINGLE"` . Isto garante que a sua aplicação irá sempre receber `ISingleAccountPublicClientApplication` , e simplifica significativamente a sua integração MSAL. O valor predefinido `"account_mode"` é de , por isso é importante alterar este valor no ficheiro `"MULTIPLE"` config se estiver a utilizar o `"single account"` modo.

Aqui está um exemplo do auth_config.jsno ficheiro **app** incluído no > diretório **principal** > **de res** > **raw** da app:

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

O modo de dispositivo partilhado permite configurar dispositivos Android para serem partilhados por vários funcionários, ao mesmo tempo que fornece a gestão apoiada pelo Microsoft Identity do dispositivo. Os colaboradores podem iniciar seduca nos seus dispositivos e aceder rapidamente à informação do cliente. Quando terminarem com o seu turno ou tarefa, poderão assinar todas as aplicações no dispositivo partilhado com um único clique e o dispositivo estará imediatamente pronto para o próximo funcionário utilizar.

Utilize `isSharedDevice()` para determinar se uma aplicação está em execução num dispositivo que se encontra em modo de dispositivo partilhado. A sua aplicação pode usar esta bandeira para determinar se deve modificar o UX em conformidade.

Aqui está um código que mostra como podes `isSharedDevice()` usar.  É da `SingleAccountModeFragment` classe na aplicação da amostra:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializar o objeto PublicClientApplication

Se configurar `"account_mode":"SINGLE"` o ficheiro config MSAL, pode lançar com segurança o objeto de aplicação devolvido como `ISingleAccountPublicCLientApplication` um .

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

### <a name="detect-single-vs-multiple-account-mode"></a>Detetar o único vs. modo de conta múltipla

Se estiver a escrever uma aplicação que só será utilizada para trabalhadores de primeira linha num dispositivo partilhado, recomendamos que escreva a sua aplicação apenas para suportar o modo de conta única. Isto inclui a maioria das aplicações focadas em tarefas, tais como aplicações de registos médicos, aplicações de faturas e a maioria das aplicações de linha de negócios. Isto simplificará o seu desenvolvimento, uma vez que muitas características do SDK não precisarão de ser acomodadas.

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

O `loadAccount` método recupera a conta do utilizador assinado. O `onAccountChanged` método determina se o utilizador inscrito mudou e, em caso afirmativo, a limpeza:

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

### <a name="globally-sign-in-a-user"></a>Globalmente assine um utilizador

Os seguintes sinais num utilizador em todo o dispositivo para outras aplicações que utilizam o MSAL com a App Autenticador:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globalmente, assine um utilizador

O seguinte remove a conta de entrada e limpa fichas em cache não só da aplicação, mas também do dispositivo que está em modo de dispositivo partilhado:

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

Os seguintes passos descrevem a configuração da sua aplicação no portal Azure e a colocação do seu dispositivo em modo de dispositivo partilhado.

### <a name="register-your-application-in-azure-active-directory"></a>Registe a sua candidatura no Diretório Ativo Azure

Primeiro, registe a sua candidatura dentro do seu inquilino organizacional. Em seguida, forneça estes valores abaixo em auth_config.jspara que a sua aplicação seja executada corretamente.

Para obter informações sobre como fazê-lo, consulte para [registar a sua candidatura](./tutorial-v2-android.md#register-your-application).

> [!NOTE]
> Quando registar a sua aplicação, utilize o guia de arranque rápido no lado esquerdo e, em seguida, selecione **Android**. Isto irá levá-lo a uma página onde lhe será pedido que forneça o Nome do **Pacote** e **Hash de Assinatura** para a sua aplicação. Estes são muito importantes para garantir que a configuração da sua aplicação funcione. Em seguida, receberá um objeto de configuração que pode usar para a sua aplicação que cortará e colará no seu auth_config.jsficheiro.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Configure a sua página de aplicativos Android no quickstart do portal Azure":::

Deve selecionar **Faça esta alteração para mim** e, em seguida, forneça os valores que o quickstart pede no portal Azure. Quando isso estiver feito, geraremos todos os ficheiros de configuração de que necessita.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Configure a sua página de projeto no quickstart do portal Azure":::

## <a name="set-up-a-tenant"></a>Configurar um inquilino

Para efeitos de teste, crie o seguinte no seu inquilino: pelo menos dois funcionários, um Administrador de Dispositivos Cloud e um Administrador Global. No portal Azure, desaprove o Administrador do Dispositivo de Nuvem modificando as Funções Organizacionais. No portal Azure, aceda às suas Funções Organizacionais selecionando O **Administrador de Diretório Ativo Azure**  >  **e**  >  **Administradores cloud Device .** Adicione os utilizadores que podem colocar um dispositivo em modo partilhado.

## <a name="set-up-an-android-device-in-shared-mode"></a>Configurar um dispositivo Android em modo partilhado

### <a name="download-the-authenticator-app"></a>Descarregue a App Authenticator

Descarregue a App autenticadora microsoft da loja Google Play. Se já tiver a aplicação descarregada, certifique-se de que é a versão mais recente.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Definições de aplicativo autenticador & registar o dispositivo na nuvem

Inicie a App Authenticator e navegue para a página principal da conta. Assim que vir a página **'Adicionar Conta',** está pronto para fazer com que o dispositivo seja partilhado.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Autenticador adicionar ecrã de conta":::

Vá ao painel **de Definições** utilizando a barra de menu da mão direita. Selecione **registo de dispositivos** no **trabalho & contas escolares**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Tela de configurações de autenticação":::

Ao clicar neste botão, ser-lhe-á pedido que autorize o acesso aos contactos do dispositivo. Isto deve-se à integração da conta do Android no dispositivo. Escolha **o permitir**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Autenticador permite ecrã de confirmação de acesso":::

O Administrador de Dispositivos Cloud deve introduzir o seu e-mail organizacional ou **registar-se como um dispositivo partilhado**. Em seguida, clique no **botão** do dispositivo partilhado e introduza as suas credenciais.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Tela de registo do dispositivo na aplicação":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Imagem de imagem mostrando a página de sposição da Microsoft":::

O dispositivo encontra-se agora em modo partilhado.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="Ecrã de aplicativo mostrando modo de dispositivo compartilhado ativado":::

 Quaisquer inserções e inscrições no dispositivo serão globais, o que significa que se aplicam a todas as aplicações integradas com MSAL e Microsoft Authenticator no dispositivo. Agora pode implementar aplicações para o dispositivo que utilizam funcionalidades do modo dispositivo partilhado.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Veja o dispositivo partilhado no portal Azure

Uma vez colocado um dispositivo em modo partilhado, torna-se conhecido da sua organização e é rastreado no seu inquilino organizacional. Pode ver os seus dispositivos partilhados olhando para o **Tipo de Junção** na lâmina do Diretório Ativo Azure do seu portal Azure.

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Todos os dispositivos pane mostrados no portal Azure":::

## <a name="running-the-sample-app"></a>Executando o aplicativo de amostra

A Aplicação sample é uma aplicação simples que vai ligar para a API graph da sua organização. Na primeira execução, será solicitado o consentimento, uma vez que o pedido é novo na sua conta de colaborador.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Ecrã de informação de configuração de aplicação":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o trabalho com a Microsoft Authentication Library e o modo de dispositivo partilhado em dispositivos Android:

> [!div class="nextstepaction"]
> [Modo de dispositivo partilhado para dispositivos Android](msal-android-shared-devices.md)
