---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 269cadb68a0e27a7d329307871ee0ccd263abf31
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424980"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-javascript)
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Faça o upload de um ficheiro de origem para uma bolha de Azure](../../../../../../storage/blobs/storage-quickstart-blobs-portal.md)

## <a name="create-a-new-js-file"></a>Criar um novo ficheiro JS

O primeiro passo é garantir que o seu projeto está aberto no seu editor favorito.

Ligue para o seu arquivo index.js.

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto.

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Invólucros JSON

Como a API rest aceita pedidos em formato JSON e também retorno resulta em JSON.
Para tornar os pedidos e respostas mais fáceis de entender, declararemos algumas aulas para usar para serializar/deserizar o JSON.


```JavaScript
class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}
```

## <a name="create-an-initial-transcription-request"></a>Crie um pedido inicial de transcrição.
Em seguida, geraremos o pedido de transcrição.

```JavaScript
const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}
```

## <a name="send-the-transcription-request"></a>Envie o pedido de transcrição.
Agora publicamos o pedido ao serviço de Discurso e verificamos o código de resposta inicial. Este código de resposta apenas indicará se o serviço recebeu o pedido. O serviço devolverá um Url nos cabeçalhos de resposta que é o local onde irá armazenar o estado de transcrição.

Depois, vamos chamar um método `CheckTranscriptionStatus` para verificar o estado e eventualmente imprimir os resultados. Vamos implementar a `CheckTranscriptionStatus` seguir.

```JavaScript
const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="check-the-requests-status"></a>Verifique o estado dos pedidos
Uma vez que o serviço processa a transcrição assíncroneamente, precisamos de sondar o seu estatuto de vez em quando. Vamos verificar a cada 5 segundos.

Podemos verificar o estado recuperando o conteúdo no Url que obtivemos quando o pedido foi publicado. Quando recuperarmos o conteúdo, deserizamo-lo numa das nossas aulas de ajudante para facilitar a interação.

Aqui está o código de sondagens com exibição de estado para tudo, exceto uma conclusão bem sucedida, vamos fazê-lo a seguir. 

`CheckTranscriptionStatus` toma o URL de estado do pedido de transcrição e sonda-o a cada 5 segundos até que indique sucesso ou erro. Em seguida, chama `PrintResults` para imprimir os resultados da transcrição. Vamos implementar a `PrintResults` seguir.
```csharp
function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}
```

## <a name="display-the-transcription-results"></a>Mostrar os resultados da transcrição
Uma vez que o serviço tenha concluído com sucesso a transcrição, os resultados serão armazenados em outro Url que podemos obter da resposta de estado. Aqui fazemos um pedido para descarregar esses resultados em um ficheiro temporário antes de lê-los e desseecializá-los.
Assim que os resultados estiverem carregados, podemos imprimi-los à consola. 

```JavaScript
function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}
```

## <a name="check-your-code"></a>Verifique o seu código
Neste ponto, o seu código deve ser assim:

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";

class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}

const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}

function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}

function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}

const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="run-your-app"></a>Executar a aplicação

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço Speech.

**Inicie a sua aplicação** - Executar index.js de nó.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]