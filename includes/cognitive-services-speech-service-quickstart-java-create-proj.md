---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 3c88b8cb40660327d2a4c28cc80b7ebd5956b189
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467252"
---
1. Inicie o Eclipse.

1. No Iniciador do Eclipse, no campo **Área de Trabalho**, insira o nome de um novo diretório de área de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche a tela de **boas-vindas** , se houver uma.

1. Na barra de menus do Eclipse, crie um novo projeto, ao escolher **Ficheiro** > **Novo** > **Projeto**.

1. Aparece a caixa de diálogo **Novo Projeto**. Selecione **Projeto Java** e selecione **Seguinte**.

   ![Captura de ecrã da caixa de diálogo Novo Projeto, com o Projeto Java realçado](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. O assistente de **novo projeto Java** é iniciado. No campo **Nome do projeto**, introduza **início rápido** e escolha **JavaSE-1.8** como o ambiente de execução. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Se a janela **Abrir Perspetiva Associada?** for apresentada, selecione **Abrir Perspetiva**.

1. Na **Explorador de pacotes**, clique com o botão direito do rato no projeto **início rápido**. Escolha **Configurar** > **Converter em Projeto Maven** no menu de contexto.

   ![Captura de ecrã do Explorador de pacotes](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. A janela **Criar Nova POM** é apresentada. No campo **ID do grupo** , insira *com. Microsoft. cognitivaservices. Speech. Samples*e, no campo **ID do artefato** , insira *início rápido*. Em seguida, selecione **Concluir**.

   ![Captura de ecrã da janela Criar nova POM](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Abra o ficheiro *pom.xml* e edite-o.

   * No final do ficheiro, antes da tag de fecho `</project>`, crie um elemento `repositories` com uma referência para o repositório Maven do SDK de Voz, conforme mostrado aqui:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Além disso, adicione um elemento `dependencies`, com o SDK de fala versão 1.7.0 como uma dependência:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Guarde as alterações.
