---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400783"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso azure speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="source-code"></a>Código de origem

Para adicionar uma nova classe vazia ao seu projeto Java, selecione **File** > **New** > **Class**. Na janela **Nova Classe Java**, introduza **speechsdk.quickstart** no campo **Pacote**, e **Principal** no campo **Nome**.

![Captura de ecrã da janela Nova Classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

Substitua o conteúdo do ficheiro *Main.java* pelo seguinte corte:

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Construir e executar app

Prima <kbd>F11</kbd>, ou selecione **Executar** > **Debug**.
Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

