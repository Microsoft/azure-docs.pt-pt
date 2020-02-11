---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: d0ab828a2574914c511042a16704c4b553960b84
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119697"
---
1. Inicie o Eclipse.

1. No Iniciador do Eclipse, no campo **Área de Trabalho**, insira o nome de um novo diretório de área de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã **welcome** se estiver presente.

1. Na barra de menus do Eclipse, crie um novo projeto, ao escolher **Ficheiro** > **Novo** > **Projeto**.

1. Aparece a caixa de diálogo **Novo Projeto**. Selecione **Projeto Java** e selecione **Seguinte**.

   ![Captura de ecrã da caixa de diálogo Novo Projeto, com o Projeto Java realçado](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. O feiticeiro do **Projeto New Java** começa. No campo **Nome do projeto**, introduza **início rápido** e escolha **JavaSE-1.8** como o ambiente de execução. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Se a janela **Abrir Perspetiva Associada?** for apresentada, selecione **Abrir Perspetiva**.

1. Na **Explorador de pacotes**, clique com o botão direito do rato no projeto **início rápido**. Escolha **Configurar** > **Converter em Projeto Maven** no menu de contexto.

   ![Captura de ecrã do Explorador de pacotes](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. A janela **Criar Nova POM** é apresentada. No campo **Group Id,** insira *com.microsoft.cognitiveservices.speech.samples*, e no campo **Artifact Id,** entre em *quickstart*. Em seguida, selecione **Concluir**.

   ![Captura de ecrã da janela Criar nova POM](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Abra o ficheiro *pom.xml* e edite-o.

   * No final do ficheiro, antes da tag de fecho `</project>`, crie um elemento `repositories` com uma referência para o repositório Maven do SDK de Voz, conforme mostrado aqui:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Adicione também um elemento `dependencies`, com a versão 1.9.0 do Speech SDK como dependência:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Guarde as alterações.
