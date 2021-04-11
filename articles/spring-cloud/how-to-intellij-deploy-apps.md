---
title: 'Tutorial: Use IntelliJ para implementar aplicações Azure Spring Cloud'
description: Utilize o IntelliJ para implementar aplicações para a Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 64ff778f9735c690f4622402ef2e3124185fa3ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879193"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Utilizar o IntelliJ para implementar aplicações do Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

O plug-in IntelliJ para Azure Spring Cloud suporta a implementação da aplicação a partir do IntelliJ IDEA.  

Antes de executar este exemplo, pode experimentar o [arranque básico](spring-cloud-quickstart.md).

## <a name="prerequisites"></a>Pré-requisitos
* [IntelliJ IDEA, Community/Ultimate Edition, versão 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Instale o plug-in
Pode adicionar o Kit de Ferramentas Azure para IntelliJ IDEA 3.43.0 da UI IntelliJ **Plugins.**

1. Começa o Intellij.  Se já abriu um projeto anteriormente, feche o projeto para mostrar o diálogo de boas-vindas. Selecione **Configurar** a partir do link inferior direito e, em seguida, clique em **Plugins** para abrir o diálogo de configuração plug-in e selecione **Instalar Plugins a partir do disco**.

    ![Selecione Configurar](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Procure por Azure Toolkit para IntelliJ.  Clique em **Install** (Instalar).

    ![Instalar plugin](media/spring-cloud-intellij-howto/install-plugin.png)

1. Clique **em Reiniciar IDE**.

## <a name="tutorial-procedures"></a>Procedimentos tutoriais
Os seguintes procedimentos implementam uma aplicação Hello World utilizando o IntelliJ IDEA.

* Projeto open gs-spring-boot
* Implemente para Azure Spring Cloud
* Mostrar registos de streaming

## <a name="open-gs-spring-boot-project"></a>Projeto open gs-spring-boot

1. Descarregue e desaperte o repositório de origem para este tutorial, ou clone-o usando Git: git clone https://github.com/spring-guides/gs-spring-boot.git 
1. cd em gs-spring-boot\complete.
1. Abra o diálogo IntelliJ **Welcome,** selecione **Import Project** para abrir o assistente de importação.
1. Selecione `gs-spring-boot\complete` a pasta.

    ![Projeto de Importação](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Implemente para Azure Spring Cloud
Para poder implementar no Azure tem de iniciar sôms com a sua conta Azure e escolher a sua subscrição.  Para obter informações de insusição, consulte [a Instalação e a inscrição.](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

1. Clique com o botão direito no seu projeto no explorador de projetos IntelliJ e selecione **Azure**  ->  **Deploy to Azure Spring Cloud**.

    ![Desdobre-se para Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Aceite o nome para aplicação no campo **Nome.** **O nome** refere-se à configuração, não ao nome da aplicação. Os utilizadores normalmente não precisam de alterá-lo.
1. Aceite o identificador do projeto para o **Artefacto.**
1. Selecione **App:** em seguida, clique em **Criar app...**.

    ![Implantar para Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Insira **o nome da App** e, em seguida, clique em **OK**.

    ![Implementar para Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Inicie a implementação clicando no botão **Executar.** 

    ![Implantar para Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. O plug-in executará o comando `mvn package` do projeto e, em seguida, criará a nova app e implantará o jarro gerado pelo `package` comando.

1. Se o URL da aplicação não for mostrado na janela de saída, obtenha-o a partir do portal Azure. Navegue do seu grupo de recursos para o exemplo de Azure Spring Cloud.  Em seguida, clique em **Apps**.  A aplicação de execução será listada.

    ![Obtenha URL de teste](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navegue para o URL no navegador.

    ![Navegue no Navegador 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Mostrar registos de streaming
Para obter os registos:
1. Selecione **Azure Explorer,** em **seguida, Cloud primavera**.
1. Clique com o botão direito na aplicação de execução.
1. Selecione **'Registos** de streaming' da lista de drop-down.

    ![Selecione registos de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selecione a instância.

    ![Selecione a instância](media/spring-cloud-intellij-howto/select-instance.png)

1. O registo de streaming será visível na janela de saída.

    ![Saída de registo de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Passos seguintes
* [Preparar a aplicação primavera para Azure Spring Cloud](how-to-prepare-app-deployment.md)
* [Saiba mais sobre o Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij/)
