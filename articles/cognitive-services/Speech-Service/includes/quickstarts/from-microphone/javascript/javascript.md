---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: a110ff1db9b75287eb1b75d80003796354aba35b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035819"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de discurso azul<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta de sites

Crie uma nova pasta vazia. No caso de querer alojar o exemplo num servidor Web, certifique-se de que este pode aceder à pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Voz para JavaScript nessa pasta

Transfira o SDK de Voz como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o para a pasta recentemente criada. Isto resulta em dois ficheiros a serem desembalados, `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map` .
Este último ficheiro é opcional e é útil para depurar o código SDK.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo ficheiro na pasta, com o nome `index.html`, e abra este ficheiro com um editor de texto.

1. Crie a seguinte estrutura HTML:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-mic.html)]

## <a name="create-the-token-source-optional"></a>Criar a origem do token (opcional)

No caso de querer alojar a página Web num servidor Web, pode fornecer opcionalmente uma origem de token para a aplicação de demonstração.
Dessa forma, a chave de subscrição nunca vai sair do servidor, o que permite aos utilizadores usufruir das capacidades de voz sem introduzir qualquer código de autorização.

Crie um novo ficheiro com o nome `token.php`. Neste exemplo, assumimos que o seu servidor web suporta a linguagem de script PHP com caracóis ativados. Introduza o seguinte código:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Os tokens de autorização têm uma duração limitada.
> Este exemplo simplificado não mostra como atualizar automaticamente os tokens de autorização. Como utilizador, pode recarregar manualmente a página ou premir F5 para atualizar.

## <a name="build-and-run-the-sample-locally"></a>Criar e executar o exemplo localmente

Para iniciar a aplicação, faça duplo clique no ficheiro index.html ou abra-o com o seu browser favorito. Será apresentada uma GUI simples que lhe permite introduzir a chave de subscrição e a [região](../../../../regions.md), e acionar um reconhecimento com o microfone.

> [!NOTE]
> Este método não funciona no navegador Safari.
> No Safari, a página web da amostra precisa de ser hospedada num servidor web; O Safari não permite que sites carregados a partir de um ficheiro local utilizem o microfone.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Criar e executar o exemplo através de um servidor Web

Inicie a aplicação, abra o seu browser favorito, aponte-o para o URL público onde está alojada a pasta, introduza a [região](../../../../regions.md) e acione um reconhecimento com o microfone. Se estiver configurado, vai adquirir um token a partir da origem de token.


## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

