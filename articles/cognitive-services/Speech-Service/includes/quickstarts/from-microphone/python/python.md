---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 9798ebaa6e0382905ba7dab647eea33bda03de40
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671409"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso azure speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="source-code"></a>Código de origem

Crie um ficheiro chamado *quickstart.py* e colhe o seguinte código Python nele.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Construir e executar app

Agora está pronto para testar a aplicação e verificar a funcionalidade de reconhecimento de voz utilizando o serviço De Discurso.

1. **Inicie a sua aplicação** - A partir da linha de comando, escreva:
    ```bash
    python quickstart.py
    ```
2. **Iniciar o reconhecimento** - Vai instá-lo a falar uma frase em inglês. O seu discurso é enviado para o serviço da Fala, transcrito como texto, e renderizado na consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](../footer.md)]
