---
title: 'Início rápido: Python de API de voz do tradutor'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para o ajudar a começar a utilizar rapidamente a API de Voz do Microsoft Translator.
services: cognitive-services
author: v-jaswel
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: v-jaswel
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a6afc4dfb25a2a5f6e778fbda877a93269a96eb
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673280"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Início rápido: Voz do Translator API com Python
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Este artigo mostra como utilizar a API de Voz do Microsoft Translator para traduzir linguagem falada num ficheiro .wav.

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar do [Python 3.x](https://www.python.org/downloads/) para executar este código.

Terá de instalar o [pacote websocket-client](https://pypi.python.org/pypi/websocket-client) para Python.

Vai necessitar de ter um ficheiro .wav denominado "speak.wav" na mesma pasta que o executável e que pode compilar a partir do código abaixo. Este ficheiro .wav deve estar em PCM padrão, em formato mono de 16 bits e 16 kHz.

Tem de ter uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API de Voz do Microsoft Translator**. Necessita de uma chave de subscrição paga a partir do [dashboard do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduzir voz

O código a seguir converte voz de um idioma para outro.

1. Crie um novo projeto de Python com o seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua subscrição.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
```

**Traduzir resposta de voz**

Um resultado com êxito é a criação de um ficheiro denominado "speak2.wav". O ficheiro contém a tradução da linguagem falada em "speak.wav".

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial da Tradução de Voz](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Consulte também

[Descrição geral da Tradução de Voz](../overview.md)
[Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
