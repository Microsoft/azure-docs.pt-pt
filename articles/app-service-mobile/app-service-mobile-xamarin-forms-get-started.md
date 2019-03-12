---
title: Introdução às Aplicações Móveis com o Xamarin.Forms
description: Siga este tutorial para começar a utilizar Aplicações Móveis para desenvolvimento do Xamarin.Forms
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 2e0a4b51e9140b9b3c0032980b7c6cd5a4d55638
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761400"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Criar uma aplicação Xamarin.Forms com o Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end com base na cloud a uma aplicação móvel Xamarin.Forms com a funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure como o back-end. Pode criar tanto um novo back-end das Aplicações Móveis assim como uma simples aplicação Xamarin.Forms de uma lista de tarefas que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Forms.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais informações, veja [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/) (Avaliação Gratuita do Azure).

* Ferramentas do Visual Studio para Xamarin, no Visual Studio 2017 ou Visual Studio para Mac. Veja a [página de instalação do Xamarin][Install Xamarin] para obter instruções.

* (opcional) Para compilar uma aplicação iOS, precisa de um Mac com Xcode 9.0 ou posterior. O Visual Studio para Mac pode servir para desenvolver aplicações iOS ou pode utilizar o Visual Studio 2017 (desde que o Mac esteja disponível na rede).

## <a name="create-a-new-mobile-apps-back-end"></a>Criar um novo back-end de Aplicações Móveis

Para criar um novo back-end das Aplicações Móveis, faça o seguinte:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Definiu agora um back-end de Aplicação Móvel que as suas aplicações móveis podem utilizar. Em seguida, transfira um projeto de servidor para um back-end de lista de tarefas simples e, em seguida, publique-o no Azure.

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor

Para configurar o projeto de servidor para utilizar o back-end Node.js ou .NET., faça o seguinte:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Transferir e executar a aplicação Xamarin.Forms

As Ferramentas do Visual Studio para Xamarin são precisas para abrir a solução, veja as [instruções de instalação do Xamarin][Install Xamarin]. Se as ferramentas já estiverem instaladas, siga estes passos para transferir e abrir a solução:

### <a name="visual-studio"></a>Visual Studio

1. Aceda ao [Portal do Azure].

2. No painel de definições da Aplicação Móvel, clique em **Início rápido** (em Implementação) > **Xamarin.Forms**. No passo 3, clique em **Criar uma nova aplicação**, se a opção ainda não estiver selecionada.  Em seguida, clique no botão **Transferir**.

   Esta ação transfere um projeto que contém uma aplicação cliente que está ligada à sua aplicação móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

3. Extraia o projeto que transferiu e, em seguida, abra-o no Visual Studio 2017.

   ![Projeto extraído no Visual Studio][8]

4. Siga as instruções abaixo para executar os projetos Android ou Windows; e, se existir um computador Mac em rede disponível, o projeto iOS.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Aceda ao [Portal do Azure].

2. No painel de definições da Aplicação Móvel, clique em **Início rápido** (em Implementação) > **Xamarin.Forms**. No passo 3, clique em **Criar uma nova aplicação**, se a opção ainda não estiver selecionada.  Em seguida, clique no botão **Transferir**.

   Esta ação transfere um projeto que contém uma aplicação cliente que está ligada à sua aplicação móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.

3. Extraia o projeto que transferiu e, em seguida, abra-o no Visual Studio para Mac.

   ![Projeto extraído no Visual Studio para Mac][9]

4. Siga as instruções abaixo para executar os projetos Android ou iOS.



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



Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição (**+**).

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



Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição (**+**).

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



Na aplicação, digite um texto significativo, como *Saber mais sobre o Xamarin*, e selecione o sinal de adição (**+**).

Esta ação envia um pedido post para o novo back-end das Aplicações Móveis que está alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens que estão armazenados na tabela são devolvidos pelo back-end das Aplicações Móveis e os dados são apresentados na lista.

![aplicação de tarefas UWP][12]

> [!NOTE]
> Vai encontrar o código que acede ao back-end da Aplicação Móvel no ficheiro **TodoItemManager.cs** em C# do projeto da biblioteca de classe portátil da sua solução.
>

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver algum problema ao criar a solução, execute o gestor de pacotes NuGet e atualize para versão mais recente do `Xamarin.Forms`, e, no projeto Android, atualize os pacotes de suporte `Xamarin.Android`. Os projetos do início rápido nem sempre incluem as versões mais recentes.

Note que todos os pacotes de suporte referenciados no projeto Android têm de ter a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

## <a name="next-steps"></a>Passos Seguintes

* [Adicione autenticação à sua aplicação](app-service-mobile-xamarin-forms-get-started-users.md) Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* [Adicione notificações push à sua aplicação](app-service-mobile-xamarin-forms-get-started-push.md) Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Hubs de Notificação do Azure para enviar as notificações push.

* [Ativar sincronização offline na sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md) Saiba como adicionar suporte offline à aplicação com um back-end de Aplicações Móveis. Com a sincronização offline, pode ver, adicionar ou modificar dados da sua aplicação móvel, mesmo quando não existe nenhuma ligação de rede.

* [Utilize o cliente gerido para as Aplicações Móveis](app-service-mobile-dotnet-how-to-use-client-library.md) Saiba como trabalhar com o SDK cliente gerido na aplicação Xamarin.

* [Utilizar outros serviços do Azure com o Xamarin.Forms](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Adicionar funcionalidades adicionais do Azure, como pesquisa, armazenamento e serviços cognitivos a aplicações Xamarin.Forms.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/
