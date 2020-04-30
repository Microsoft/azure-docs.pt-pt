---
title: Tutorial - Use intelliJ para implementar aplicações Azure Spring Cloud
description: Utilize o IntelliJ para implementar aplicações para a Nuvem de primavera Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 8e473a5692c3fa2be3b0f2d823d2a36ba768d661
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81731363"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Utilizar o IntelliJ para implementar aplicações do Azure Spring Cloud
O plug-in IntelliJ para Azure Spring Cloud suporta a implementação da aplicação a partir do IntelliJ IDEA.  

## <a name="prerequisites"></a>Pré-requisitos
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA, Community/Ultimate Edition, versão 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Instale o plug-in
Pode adicionar o Kit de Ferramentas Azure para IntelliJ IDEA 3.35.0 do IntelliJ **Plugins** UI.

1. Começa o IntelliJ.  Se já abriu um projeto anteriormente, encerre o projeto para mostrar o diálogo de boas-vindas. **Selecione Configurar** a partir do link inferior direito e, em seguida, clique em **Plugins** para abrir o diálogo de configuração plug-in e selecione **Instale Plugins a partir do disco**.

    ![Selecione Configurar](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Procure o Kit de Ferramentas Azure para intelliJ.  Clique em **Instalar**.

    ![Instalar plugin](media/spring-cloud-intellij-howto/install-plugin.png)

1. Clique em **Reiniciar IDE**.

## <a name="tutorial-procedures"></a>Procedimentos tutoriais
Os seguintes procedimentos implementam uma aplicação Hello World utilizando o IntelliJ IDEA.

* Projeto open gs-spring-boot
* Desdobre para azure nuvem de primavera
* Mostrar registos de streaming

## <a name="open-gs-spring-boot-project"></a>Projeto open gs-spring-boot

1. Descarregue e desaperte o repositório de origem para este tutorial, ou clone-o usando Git: git clonehttps://github.com/spring-guides/gs-spring-boot.git 
1. cd em gs-spring-boot\completo.
1. Abra o diálogo de **boas-vindas** intelliJ, selecione **Import Project** para abrir o assistente de importação.
1. Selecione `gs-spring-boot\complete` pasta.

    ![Projeto de Importação](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Desdobre para azure nuvem de primavera
Para ser implementado no Azure, tem de iniciar sessão com a sua conta Azure e escolher a sua subscrição.  Para obter mais detalhes de iniciar sessão, consulte [Instalação e iniciar sessão](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Clique no seu projeto no explorador de projetos IntelliJ e selecione **Azure** -> **Deploy para Azure Spring Cloud**.

    ![Desdobre para Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Aceite o nome da app no campo **Nome.** **O nome** refere-se à configuração, não ao nome da aplicação. Os utilizadores normalmente não precisam de o alterar.
1. Aceite o identificador do projeto para o **Artefacto.**
1. Selecione **App:** em seguida, clique em **Criar app...**.

    ![Desdobre para Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Introduza o nome da **aplicação**e, em seguida, clique **OK**.

    ![Desdobre para Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Inicie a implementação clicando no botão **Executar.** 

    ![Desdobre para Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. O plug-in executará `mvn package` o comando do projeto e, em seguida, `package` criará a nova aplicação e implementará o jarro gerado pelo comando.

1. Se o URL da aplicação não for mostrado na janela de saída, obtenha-o no portal Azure. Navegue do seu grupo de recursos para a instância de Azure Spring Cloud.  Em seguida, clique em **Apps**.  A aplicação de execução será listada.

    ![Obtenha URL de teste](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navegue para o URL no navegador.

    ![Navegue no Navegador 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Mostrar registos de streaming
Para obter os registos:
1. Selecione **Azure Explorer,** em **seguida, Nuvem de primavera**.
1. Clique na aplicação de execução.
1. Selecione **Registos** de Streaming da lista de lançamentos.

    ![Selecione registos de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selecione a instância.

    ![Selecione instância](media/spring-cloud-intellij-howto/select-instance.png)

1. O registo de streaming será visível na janela de saída.

    ![Saída de registo de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Passos seguintes
* [Prepare a aplicação da primavera para a Nuvem de primavera Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Saiba mais sobre o Kit de Ferramentas Azure para o IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
