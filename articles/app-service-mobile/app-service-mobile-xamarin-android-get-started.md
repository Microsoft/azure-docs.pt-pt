---
title: Introdução às Mobile Apps do Azure para aplicações Xamarin.Android
description: Siga este tutorial para começar a utilizar Mobile Apps do Azure para desenvolvimento do Xamarin.Android
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 038ca0bd7690ac90470d9a739a47d0f634f1297b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388482"
---
# <a name="create-a-xamarinandroid-app"></a>Criar uma Aplicação Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Xamarin.Android. Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md).

Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações Xamarin.Android.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma conta, inscreva-se para uma versão de avaliação do Azure e obtenha até 10 Aplicações Móveis gratuitas. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio com Xamarin. Para obter instruções, consulte [Configuração e instalação do Visual Studio e Xamarin](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end da Aplicação Móvel do Azure
Siga estes passos para criar um back-end da Aplicação Móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, transfira um projeto de servidor para um back-end simples de uma “lista de tarefas” e publique-o no Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Executar o aplicativo Xamarin. Android
1. Abra o projeto Xamarin. Android.

2. Vá para a [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na folha `Overview`, procure a URL que é o ponto de extremidade público para seu aplicativo móvel. Exemplo – o sitename para o nome do meu aplicativo "test123" será https://test123.azurewebsites.net.

3. Abra o arquivo `ToDoActivity.cs` nesta pasta-xamarin. Android/ZUMOAPPNAME/ToDoActivity. cs. O nome do aplicativo é `ZUMOAPPNAME`.

4. Na classe `ToDoActivity`, substitua a variável `ZUMOAPPURL` pelo ponto de extremidade público acima.

    `const string applicationURL = @"ZUMOAPPURL";`

    ficará
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Pressione a tecla F5 para implantar e executar o aplicativo.

6. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, e clique no botão **Adicionar**.

    ![][10]

    Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

   > [!NOTE]
   > Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados (presente no ficheiro ToDoActivity.cs c#).
   
## <a name="troubleshooting"></a>Resolução de problemas
Se tiver algum problema ao criar a solução, execute o gestor de pacotes NuGet e atualize os pacotes de suporte `Xamarin.Android`. Os projetos do início rápido nem sempre incluem as versões mais recentes.

Note que todos os pacotes de suporte referenciados no projeto têm de ter a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
