---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2a7734f729c4b1db7e8c0b4571e8792373ee11ae
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203211"
---
## <a name="set-up-your-project"></a>Configurar seu projeto

Esta secção vai criar um novo projeto para demonstrar como integrar uma aplicação .NET de Desktop do Windows (XAML) com *início de sessão com a Microsoft* para que o aplicativo pode consultar as APIs da Web que exigem um token.

A aplicação que criou com este Guia apresenta um botão que é utilizado para chamar um gráfico, uma área para mostrar os resultados na tela e um botão para terminar sessão.

> [!NOTE]
> Prefere transferir o projeto do Visual Studio este exemplo em vez disso? [Transfira um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)e avançar para o [passo de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.
>

Para criar a sua aplicação, faça o seguinte:

1. No Visual Studio, selecione **arquivo** > **New** > **projeto**.
2. Sob **modelos**, selecione **Visual c#**.
3. Selecione **WPF App (.NET Framework)**, consoante a versão da versão do Visual Studio que está a utilizar.

## <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. No Visual Studio, selecione **Ferramentas** > **Gestor de Pacotes NuGet**> **Consola do Gestor de Pacotes**.
2. Na janela da consola do Gestor de pacotes, cole o seguinte comando do PowerShell do Azure:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

    > [!NOTE] 
    > Este comando instala a biblioteca de autenticação da Microsoft. A MSAL lida com a aquisição, colocação em cache e atualizar os tokens de utilizador que são utilizados para aceder às APIs que estão protegidos pelo Azure Active Directory v2.0
    >

## <a name="add-the-code-to-initialize-msal"></a>Adicione o código para inicializar a MSAL

Neste passo, vai criar uma classe para lidar com a interação com MSAL, por exemplo, a manipulação de tokens.

1. Abra o *App.xaml.cs* de ficheiros e, em seguida, adicione a referência para a MSAL para a classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Atualize a classe de aplicação para o seguinte:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Criar a IU da aplicação

Esta secção mostra como um aplicativo pode consultar um servidor de back-end protegido, como o Microsoft Graph. 

R *mainwindow. XAML* ficheiro deve ser criado automaticamente como parte do seu modelo de projeto. Abrir este ficheiro e, em seguida, substitua o seu aplicativo  *\<Grid >* nó com o código a seguir:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
