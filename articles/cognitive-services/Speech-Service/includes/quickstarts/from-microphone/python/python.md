---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85839085"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de discurso azul<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="source-code"></a>Código de origem

Crie um ficheiro chamado *quickstart.py* e cole o seguinte código Python nele.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Construir e executar aplicativo

Agora estás pronto para testar o reconhecimento da fala usando o serviço de fala. 

Se estiver a executar isto no macOS e é a primeira aplicação Python que construiu e que usa um microfone, provavelmente terá de dar acesso ao Terminal ao microfone. Abra **as definições do sistema** e selecione Segurança & **Privacidade**. Em seguida, selecione **Privacidade** e **localize** o Microfone na lista. Por último, selecione **Terminal** e guarde. 

1. **Inicie a sua aplicação** - A partir da linha de comando, escreva:
    ```bash
    python quickstart.py
    ```
2. **Iniciar o reconhecimento** - Vai levá-lo a falar uma frase em inglês. O seu discurso é enviado para o serviço de discurso, transcrito como texto, e renderizado na consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
