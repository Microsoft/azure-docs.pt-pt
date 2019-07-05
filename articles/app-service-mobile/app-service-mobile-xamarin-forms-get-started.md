---
title: Introdução às Aplicações Móveis com o Xamarin.Forms
description: Siga este tutorial para começar a utilizar Aplicações Móveis para desenvolvimento do Xamarin.Forms
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: bca0f0de7de321060635459c4435525f650c7467
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446329"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Criar uma aplicação Xamarin.Forms com o Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started) hoje mesmo.
>

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end com base na cloud a uma aplicação móvel Xamarin.Forms com a funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure como o back-end. Pode criar tanto um novo back-end das Aplicações Móveis assim como uma simples aplicação Xamarin.Forms de uma lista de tarefas que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Forms.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais informações, veja [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/) (Avaliação Gratuita do Azure).

* Do Visual Studio Tools for Xamarin, no Visual Studio 2017 ou posterior, ou o Visual Studio para Mac. Consulte a [página de instalação do Xamarin][Install Xamarin] para obter instruções.

* (opcional) Para compilar uma aplicação iOS, precisa de um Mac com Xcode 9.0 ou posterior. Visual Studio para Mac pode ser usado para desenvolver aplicações iOS ou o Visual Studio 2017 ou mais tarde pode ser usado (desde que o Mac está disponível na rede).

## <a name="create-a-new-mobile-apps-back-end"></a>Criar um novo back-end de Aplicações Móveis
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação de base de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Executar a solução do xamarin. Forms

O Visual Studio Tools para Xamarin são necessárias para abrir a solução, consulte a [instruções de instalação do Xamarin][Install Xamarin]. Se as ferramentas já estiverem instaladas, siga estes passos para transferir e abrir a solução:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows e Mac)

1. Vá para o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Sobre o `Overview` painel, procure o URL que é o ponto final público para a sua aplicação móvel. Exemplo - sitename para meu nome de aplicação "test123" será https://test123.azurewebsites.net.

2. Abra o ficheiro `Constants.cs` nesta pasta - xamarin.forms/ZUMOAPPNAME. O nome da aplicação é `ZUMOAPPNAME`.

3. Na `Constants.cs` classe, substitua `ZUMOAPPURL` variável com o ponto final público acima.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    torna-se

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Siga as instruções abaixo para executar os projetos Android ou Windows; e, se existir um computador Mac em rede disponível, o projeto iOS.

## <a name="optional-run-the-android-project"></a>(Opcional) Executar o projeto Android

Nesta secção irá executar o projeto Xamarin.Android. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Android.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do rato no projeto Android (Droid) e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Compilar**, selecionar **Gestor de Configuração**.

3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** junto ao projeto Android, e certifique-se de que o projeto de código partilhado tem a caixa **Compilar** assinalada.

4. Para compilar o projeto e iniciar a aplicação no emulador do Android, prima a tecla **F5** ou clique no botão **Iniciar**.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do rato no projeto Android e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. Para compilar o projeto e iniciar a aplicação num emulador do Android, selecione o menu **Executar** e, em seguida, **Iniciar Depuração**.

Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição ( **+** ).

![Aplicação de tarefas Android][11]

Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.

> [!NOTE]
> O código que acede ao back-end da Aplicação Móvel está no ficheiro C# **TodoItemManager.cs** do projeto de código partilhado na solução.
>

## <a name="optional-run-the-ios-project"></a>(Opcional) Executar o projeto iOS

Nesta secção irá executar o projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do rato no projeto iOS e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Compilar**, selecionar **Gestor de Configuração**.

3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** junto ao projeto iOS, e certifique-se de que o projeto de código partilhado tem a caixa **Compilar** assinalada.

4. Para compilar o projeto e iniciar a aplicação no emulador do iPhone, selecione a tecla **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do rato no projeto iOS e, em seguida, selecione **Configurar como Projeto de Arranque**.

2. No menu **Executar**, selecione **Iniciar Depuração** para compilar o projeto e iniciar a aplicação no emulador do iPhone.

Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição ( **+** ).

![aplicação de tarefas iOS][10]

Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.

> [!NOTE]
> Irá encontrar o código que acede ao back-end da Aplicação Móvel está no ficheiro C# **TodoItemManager.cs** do projeto de código partilhado na solução.
>

## <a name="optional-run-the-windows-project"></a>(Opcional) Executar o projeto Windows

Nesta secção, executa o projeto da Plataforma Universal do Windows (UWP) do Xamarin.Forms para dispositivos Windows. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do rato em qualquer projeto UWP e, em seguida, selecione **Definir como Projeto de Arranque**.

2. No menu **Compilar**, selecionar **Gestor de Configuração**.

3. Na caixa de diálogo **Gestor de Configuração**, marque as caixas de verificação **Compilar** e **Implementar** junto ao projeto Windows escolhido, e certifique-se de que o projeto de código partilhado tem a caixa **Compilar** assinalada.

4. Para compilar o projeto e iniciar a aplicação no emulador do Windows, prima a tecla **F5** ou clique no botão **Iniciar** (que deve ler **Computador Local**).

> [!NOTE]
> Não pode executar o projeto Windows no macOS.

Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição ( **+** ).

Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.

![aplicação de tarefas UWP][12]

> [!NOTE]
> Vai encontrar o código que acede ao back-end da Aplicação Móvel no ficheiro **TodoItemManager.cs** em C# do projeto da biblioteca de classe portátil da sua solução.
>

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver algum problema ao criar a solução, execute o gestor de pacotes NuGet e atualize para versão mais recente do `Xamarin.Forms`, e, no projeto Android, atualize os pacotes de suporte `Xamarin.Android`. Os projetos do início rápido nem sempre incluem as versões mais recentes.

Note que todos os pacotes de suporte referenciados no projeto Android têm de ter a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
