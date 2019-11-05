---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: bcf353fa0783429702143b2bf0bdf2114dd4bed6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503662"
---
## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, introduza **speechsdk.quickstart** no campo **Pacote**, e **Principal** no campo **Nome**.

   ![Captura de ecrã da janela Nova Classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo seguinte fragmento:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.
Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
