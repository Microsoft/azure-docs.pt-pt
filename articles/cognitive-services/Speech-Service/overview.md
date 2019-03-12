---
title: O que é o Serviço de Voz?
titleSuffix: Azure Cognitive Services
description: 'O serviço de voz, parte dos serviços cognitivos do Azure, une os vários serviços de voz que estavam disponíveis anteriormente em separado: Voz do Bing (que inclui o reconhecimento de voz e de texto em fala), conversão de voz personalizada e tradução de voz.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 9beab6f30d42a7fd2c424971d602a581732eb946
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543054"
---
# <a name="what-is-speech-services"></a>O que é o serviços de fala?

Como os outros serviços de voz do Azure, serviços de voz têm a tecnologia tecnologias de fala utilizadas em produtos como o Cortana e o Microsoft Office.

Os serviços de voz unam-se anteriormente disponíveis através de funcionalidades de voz do Azure a [API de voz do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [de voz do Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [voz personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home), e [personalizado Voz](https://customvoice.ai/) serviços. Agora, uma subscrição dá acesso a todas estas capacidades.

## <a name="main-speech-services-functions"></a>Funções de serviços de voz de principal

As funções principais de serviços de voz são voz em texto (também o reconhecimento de fala chamado ou transcrição), de voz (síntese de fala) e de tradução de voz.

|Função|Funcionalidades|
|-|-|
|[Conversão de voz em texto](speech-to-text.md)| <li>Transcreve voz em tempo real contínua para texto.<li>Pode fazer a transcrição em série de voz de gravações de áudio. <li>Suporta resultados intermédios, deteção de fim de voz, formatação automática de texto e máscara de linguagem inapropriada. <li>Pode recorrer a [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) para calcular a intenção do utilizador a partir da voz transcrita.\*|
|[Voz](text-to-speech.md)| <li>**NOVO**: Fornece neurais vozes de texto para discurso quase distinguir de voz humana (em inglês). <li>Converte texto em voz com som natural. <li>Oferece vários géneros e/ou dialetos para muitos idiomas suportados. <li>Suporta a introdução de texto simples ou Speech Synthesis Markup Language (SSML). |
|[Tradução de voz](speech-translation.md)| <li>Traduz áudio de transmissão em fluxo quase em tempo real.<li> Também pode processar voz gravada.<li>Fornece resultados como texto ou voz sintetizada. |


## <a name="customize-speech-features"></a>Personalizar funcionalidades de voz

Pode utilizar os seus dados para preparar os modelos subjacentes às funcionalidades de Conversão de Voz em Texto e Conversão de Texto em Voz do serviço de Voz.

|Funcionalidade|Modelo|Objetivo|
|-|-|-|
|Conversão de voz em texto|[Modelo acústico](how-to-customize-acoustic-models.md)|Ajuda a transcrever oradores e ambientes específicos, como carros ou fábricas.|
||[Modelo de linguagem](how-to-customize-language-model.md)|Ajuda a transcrever vocabulário e gramática específicos de uma área, como gíria médica ou informática.|
||[Modelo de pronúncia](how-to-customize-pronunciation.md)|Ajuda a transcrever abreviaturas e acrónimos, por exemplo, "IOU" para "i owe you". |
|Conversão de texto em voz|[Tipo de voz](how-to-customize-voice-font.md)|Dá à sua aplicação uma voz própria ao preparar o modelo em amostras de voz humana.|

Pode utilizar os seus modelos personalizados em qualquer lugar onde utilizaria os modelos padrão na funcionalidade de Conversão de Voz em Texto ou Conversão de Texto em Voz da sua aplicação.

## <a name="use-the-speech-service"></a>Utilizar o serviço de Voz

Para simplificar o desenvolvimento de aplicações preparadas para voz, a Microsoft fornece o [SDK de Voz](speech-sdk.md) para utilização com o serviço de Voz. O SDK de Voz fornece APIs nativas consistentes de Conversão de Voz em Texto e Tradução de Voz para C#, C++ e Java. Se estiver a desenvolver com uma destas linguagens, o SDK de Voz torna o desenvolvimento mais fácil ao tratar dos detalhes de rede por si.

Os serviços de voz também tem um [REST API](rest-apis.md) que funciona com qualquer linguagem de programação que possa fazer pedidos HTTP. A interface REST não oferece a funcionalidade de transmissão em fluxo e em tempo real do SDK.

|<br>Método|Voz<br>em Texto|Texto em<br>Voz|Voz<br>Tradução|<br>Descrição|
|-|-|-|-|-|
|[SDK de Voz](speech-sdk.md)|Sim|Não|Sim|APIs nativas para C#, C++ e Java para simplificar o desenvolvimento.|
|[APIs REST](rest-apis.md)|Sim|Sim|Não|Uma API simples baseada em HTTP que facilita a adição de voz às suas aplicações.|

### <a name="websockets"></a>WebSockets

Os serviços de voz também suporta protocolos de WebSocket para transmissão em fluxo de voz em texto e tradução de voz. Os SDKs de Voz utilizam estes protocolos para comunicar com o serviço de Voz. Utilize o SDK de Voz em vez de tentar implementar a sua própria comunicação de WebSocket com o serviço de Voz.

Se já tiver o código que usa de voz do Bing ou de voz do Translator por meio de WebSockets, pode atualizá-lo para utilizar os serviços de voz. Os protocolos de WebSocket são compatíveis, no entanto, os pontos finais são diferentes.

### <a name="speech-devices-sdk"></a>SDK de Dispositivos de Voz

O [SDK de Dispositivos de Voz](speech-devices-sdk.md) é uma plataforma integrada de hardware e software para programadores de dispositivos preparados para voz. O nosso parceiro de hardware fornece designs de referência e unidades de desenvolvimento. A Microsoft fornece um SDK otimizado para dispositivos que tira total partido das capacidades do hardware.


## <a name="speech-scenarios"></a>Cenários de voz

Casos de utilização para os serviços de voz incluem:

> [!div class="checklist"]
> * Criar aplicações acionadas por voz
> * Transcrever gravações de centros de atendimento telefónico
> * Implementar bots de voz

### <a name="voice-user-interface"></a>Interface de utilizador de Voz

A entrada de voz é uma excelente forma de tornar a sua aplicação flexível, automatizada e rápida de utilizar. Com uma aplicação preparada para voz, os utilizadores podem simplesmente perguntar pelas informações que pretendem.

Se a sua aplicação se destina a ser utilizada pelo público em geral, pode utilizar os modelos de reconhecimento de voz predefinidos. Reconhecem uma grande variedade de oradores em ambientes comuns.

Se a aplicação for utilizada num domínio específico, por exemplo, medicina ou TI, pode criar um [modelo de idioma](how-to-customize-language-model.md). Pode utilizar este modelo para ensinar os serviços de fala sobre a terminologia especial utilizada pela sua aplicação.

Se a aplicação for utilizada num ambiente ruidoso, como uma fábrica, pode criar um [modelo acústico](how-to-customize-acoustic-models.md) personalizado. Esse modelo ajuda os serviços de voz para distinguir voz de ruído.

### <a name="call-center-transcription"></a>Transcrição de centros de atendimento telefónico

Muitas vezes, as gravações dos centros de atendimento telefónico são consultadas apenas se surgir um problema numa chamada. Com o serviço de Voz, é fácil transcrever todas as gravações para texto. Pode facilmente indexar o texto para [pesquisa em texto completo](https://docs.microsoft.com/azure/search/search-what-is-azure-search) ou aplicar a [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) para detetar o sentimento, o idioma e expressões-chave.

Se sua gravações de centro de chamada envolvem terminologia especializada, como nomes de produtos ou gíria, IT, pode criar uma [modelo de idioma](how-to-customize-language-model.md) ensinar os serviços de fala, o vocabulário. Um personalizado [modelo acústico](how-to-customize-acoustic-models.md) pode ajuda os serviços de voz compreender as ligações de telefone aquém do ideal.

Para obter mais informações sobre este cenário, leia mais sobre a [transcrição em série](batch-transcription.md) com o serviço de Voz.

### <a name="voice-bots"></a>Bots de voz

Os [bots](https://dev.botframework.com/) são uma forma popular de ligar os utilizadores às informações que pretendem e os clientes às empresas favoritas. Ao adicionar uma interface de utilizador de conversação ao seu site ou aplicação facilita a localização da suas funcionalidades e torna o acesso às mesmas mais rápido. Com o serviço de Voz, esta conversa assume uma nova dimensão em termos de fluência ao responder a consultas faladas.

Para adicionar uma personalidade única ao seu bot preparado para voz, pode dar-lhe uma voz própria. A criação de uma voz personalizada é um processo de dois passos. Em primeiro lugar, [faça gravações](record-custom-voice-samples.md) da voz que pretende utilizar. Em seguida, [submeta essas gravações](how-to-customize-voice-font.md) (juntamente com uma transcrição de texto) para o [portal de personalização de voz](https://cris.ai/Home/CustomVoice) do serviço de Voz, que fará o resto. Depois de criar a sua voz personalizada, os passos para utilizá-la na sua aplicação são simples.

## <a name="next-steps"></a>Passos Seguintes

Obtenha uma chave de subscrição para os serviços de voz.

> [!div class="nextstepaction"]
> [Experimente gratuitamente os serviços de voz](get-started.md)
