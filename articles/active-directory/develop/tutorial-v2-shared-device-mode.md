---
title: Usando o modo de dispositivo compartilhado com o MSAL Android | Azure
description: Saiba como preparar um dispositivo Android para ser executado no modo compartilhado e executar um aplicativo de trabalho de início.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 401681b1704e36072da3854ef5b5f8e7c1e3f7b2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030864"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Tutorial: usar o modo de dispositivo compartilhado em seu aplicativo Android

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Guia para programadores

Este guia fornece orientação para desenvolvedores para implementar o modo de dispositivo compartilhado em um aplicativo Android usando a MSAL (biblioteca de autenticação da Microsoft). Consulte o [tutorial do Android para MSAL](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) para ver como integrar o MSAL ao seu aplicativo Android, conectar um usuário, chamar o Microsoft Graph e sair de um usuário.

### <a name="download-the-sample"></a>Transferir o exemplo

Clone o [aplicativo de exemplo](https://github.com/Azure-Samples/ms-identity-android-java/) do github. O exemplo tem a capacidade de trabalhar no [modo de conta única ou de várias contas](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Adicionar o SDK do MSAL ao seu repositório Maven local

Se você não estiver usando o aplicativo de exemplo, adicione a biblioteca MSAL como uma dependência no arquivo Build. gradle, desta forma:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Configurar seu aplicativo para usar o modo de dispositivo compartilhado

Consulte a [documentação de configuração](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) para obter mais informações sobre como configurar o arquivo de configuração.

Defina `"shared_device_mode_supported"` como `true` no arquivo de configuração do MSAL.

Talvez você não esteja planejando dar suporte ao modo de várias contas. Isso pode ser se você não estiver usando um dispositivo compartilhado e o usuário puder entrar no aplicativo com mais de uma conta ao mesmo tempo. Nesse caso, defina `"account_mode"` como `"SINGLE"`. Isso garante que seu aplicativo sempre obterá `ISingleAccountPublicClientApplication`e simplifica significativamente a integração do MSAL. O valor padrão de `"account_mode"` é `"MULTIPLE"`, portanto, é importante alterar esse valor no arquivo de configuração se você estiver usando o modo de `"single account"`.

Aqui está um exemplo do arquivo auth_config. JSON incluído no **aplicativo**>**principal**>**res**>diretório **bruto** do aplicativo de exemplo:

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

### <a name="detect-shared-device-mode"></a>Detectar o modo de dispositivo compartilhado

O modo de dispositivo compartilhado permite configurar dispositivos Android para serem compartilhados por vários funcionários, fornecendo ao mesmo tempo o gerenciamento de identidades da Microsoft do dispositivo. Os funcionários podem entrar em seus dispositivos e acessar as informações do cliente rapidamente. Quando eles forem concluídos com sua mudança ou tarefa, eles poderão sair de todos os aplicativos no dispositivo compartilhado com um único clique e o dispositivo estará imediatamente pronto para uso do próximo funcionário.

Use `isSharedDevice()` para determinar se um aplicativo está em execução em um dispositivo que está no modo de dispositivo compartilhado. Seu aplicativo pode usar esse sinalizador para determinar se ele deve modificar o UX adequadamente.

Aqui está um trecho de código que mostra como você pode usar `isSharedDevice()`.  É da classe `SingleAccountModeFragment` no aplicativo de exemplo:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializar o objeto PublicClientApplication

Se você definir `"account_mode":"SINGLE"` no arquivo de configuração MSAL, poderá converter com segurança o objeto de aplicativo retornado como um `ISingleAccountPublicCLientApplication`.

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

### <a name="detect-single-vs-multiple-account-mode"></a>Detectar modo único versus várias contas

Se você estiver escrevendo um aplicativo que será usado apenas para os primeiros trabalhadores em um dispositivo compartilhado, recomendamos que você escreva seu aplicativo para dar suporte apenas ao modo de conta única. Isso inclui a maioria dos aplicativos que são focados em tarefas, como os aplicativos de registros médicos, aplicativos de fatura e a maioria dos aplicativos de linha de negócios. Isso simplificará o desenvolvimento, pois muitos recursos do SDK não precisarão ser acomodados.

Se seu aplicativo dá suporte a várias contas, bem como ao modo de dispositivo compartilhado, você deve executar uma verificação de tipo e converter para a interface apropriada, conforme mostrado abaixo.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obter o usuário conectado e determinar se um usuário foi alterado no dispositivo

O método `loadAccount` recupera a conta do usuário conectado. O método `onAccountChanged` determina se o usuário conectado foi alterado e, nesse caso, limpa:

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

### <a name="globally-sign-in-a-user"></a>Conectar-se globalmente a um usuário

Os seguintes sinais em um usuário em todo o dispositivo para outros aplicativos que usam o MSAL com o aplicativo autenticador:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Desconectar um usuário globalmente

O a seguir remove a conta conectada e limpa os tokens em cache do aplicativo, mas também do dispositivo que está no modo de dispositivo compartilhado:

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

As etapas a seguir descrevem como configurar seu aplicativo no portal do Azure e colocar seu dispositivo no modo de dispositivo compartilhado.

### <a name="register-your-application-in-azure-active-directory"></a>Registrar seu aplicativo no Azure Active Directory

Primeiro, Registre seu aplicativo em seu locatário organizacional. Em seguida, forneça esses valores abaixo em auth_config. JSON para que seu aplicativo seja executado corretamente.

Para obter informações sobre como fazer isso, consulte [registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Ao registrar seu aplicativo, use o guia de início rápido no lado esquerdo e, em seguida, selecione **Android**. Isso levará você a uma página em que será solicitado que você forneça o **nome do pacote** e o **hash de assinatura** para seu aplicativo. Eles são muito importantes para garantir que a configuração do aplicativo funcione. Em seguida, você receberá um objeto de configuração que pode ser usado para seu aplicativo que será recortado e colado em seu arquivo auth_config. JSON.

![tela de registro do aplicativo](media/tutorial-v2-shared-device-mode/register-app.png) você deve selecionar **fazer essa alteração para mim** e, em seguida, fornecer os valores que o início rápido solicita no portal do Azure. Quando isso for feito, geraremos todos os arquivos de configuração necessários.

![Tela de informações de configuração do aplicativo](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Configurar um inquilino

Para fins de teste, configure o seguinte em seu locatário: pelo menos dois funcionários, um administrador de dispositivo de nuvem e um administrador global. No portal do Azure, defina o administrador do dispositivo de nuvem modificando funções organizacionais. No portal do Azure, acesse suas funções organizacionais selecionando Azure Active Directory **funções de > e administradores** > **administrador de dispositivo de nuvem**. Adicione os usuários que podem colocar um dispositivo no modo compartilhado.

## <a name="set-up-an-android-device-in-shared-mode"></a>Configurar um dispositivo Android no modo compartilhado

### <a name="download-the-authenticator-app"></a>Baixar o aplicativo autenticador

Baixe o aplicativo Microsoft Authenticator da Google Play Store. Se você já tiver baixado o aplicativo, verifique se ele é a versão mais recente.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Configurações do aplicativo autenticador & registrar o dispositivo na nuvem

Inicie o aplicativo autenticador e navegue até a página da conta principal. Depois de ver a página **adicionar conta** , você estará pronto para tornar o dispositivo compartilhado.

![Tela de adição de conta do autenticador](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Vá para o painel **configurações** usando a barra de menus à direita. Selecione **registro de dispositivo** em **contas de trabalho & escola**.
 
 ![Tela de adição de conta do autenticador](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Ao clicar nesse botão, você será solicitado a autorizar o acesso aos contatos do dispositivo. Isso ocorre devido à integração da conta do Android no dispositivo. Escolha **permitir**.

 ![Tela de adição de conta do autenticador](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

O administrador do dispositivo de nuvem deve inserir seu email organizacional em **ou registrar-se como um dispositivo compartilhado**. Em seguida, clique no botão **registrar como dispositivo compartilhado** e insira suas credenciais.

![tela de registro – dispositivo](media/tutorial-v2-shared-device-mode/register-device.png)

![iniciar sessão](media/tutorial-v2-shared-device-mode/sign-in.png)

O dispositivo agora está no modo compartilhado.

![tela de registro – dispositivo](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 As entradas e entradas no dispositivo serão globais, o que significa que se aplicam a todos os aplicativos integrados ao MSAL e Microsoft Authenticator no dispositivo. Agora você pode implantar aplicativos no dispositivo que usam recursos de modo de dispositivo compartilhado.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Exibir o dispositivo compartilhado no portal do Azure

Depois de colocar um dispositivo no modo compartilhado, ele se torna conhecido em sua organização e é acompanhado em seu locatário organizacional. Você pode exibir seus dispositivos compartilhados examinando o **tipo de junção** na folha Azure Active Directory de seu portal do Azure.

![Folha todos os dispositivos na portal do Azure](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Executando o aplicativo de exemplo

O aplicativo de exemplo é um aplicativo simples que chamará o API do Graph de sua organização. n primeira execução você será solicitado a consentir, pois o aplicativo é novo na sua conta de funcionário.

![Tela de informações de configuração do aplicativo](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o modo compartilhado no [modo de dispositivo compartilhado para dispositivos Android](shared-device-mode.md)