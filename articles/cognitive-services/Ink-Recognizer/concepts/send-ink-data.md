---
title: Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API do analisador de tinta para aplicativos diferentes
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: a5fe77ace5f745911bb9085dd6996a8d21fe265f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879043"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital 

Tinta digital refere-se às tecnologias que permitem representações digitais de entrada, como escrita manual e desenhos. Normalmente, isso é obtido usando um digitalizador que captura os movimentos dos dispositivos de entrada, como uma caneta. À medida que os dispositivos continuam a permitir experiências avançadas de tinta digital, a inteligência artificial e a aprendizagem automática permitem o reconhecimento de texto e formas escritas em qualquer contexto. A API do reconhecedor de tinta permite que você envie traços de tinta e obtenha informações detalhadas sobre eles. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>A API do reconhecedor de tinta vs. Serviços de OCR

A API do reconhecedor de tinta não usa o OCR (reconhecimento óptico de caracteres). Os serviços de OCR processam os dados de pixel de imagens para fornecer manuscrito e reconhecimento de texto. Isso às vezes é chamado de reconhecimento offline. Em vez disso, a API do reconhecedor de tinta requer dados de traço de tinta digital que são capturados conforme o dispositivo de entrada é usado. O processamento de dados de tinta digital dessa maneira pode produzir resultados de reconhecimento mais precisos em comparação com os serviços de OCR. 

## <a name="sending-ink-data"></a>Enviando dados de tinta

A API do reconhecedor de tinta requer as coordenadas X e Y que representam os traços de tinta criados por um dispositivo de entrada, desde o momento em que ela toca a superfície de detecção quando ela é levantada. Os pontos de cada traço devem ser uma cadeia de caracteres de valores separados por vírgula e ser formatados em JSON, como o exemplo a seguir. Além disso, cada traço de tinta deve ter uma ID exclusiva em cada solicitação. Se a ID for repetida dentro da mesma solicitação, a API retornará um erro. Para obter os resultados de reconhecimento mais precisos, tenha pelo menos oito dígitos após o ponto decimal. Pressupõe-se que a origem (0,0) da tela seja o canto superior esquerdo da tela de escrita à tinta.

> [!NOTE]
> O exemplo a seguir não é um JSON válido. Você pode encontrar uma solicitação JSON de reconhecedor de tinta completa no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Resposta do reconhecedor de tinta

A API do reconhecedor de tinta retorna uma resposta de análise sobre os objetos reconhecidos do conteúdo de tinta. A resposta contém unidades de reconhecimento que descrevem as relações entre diferentes traços de tinta. Por exemplo, os traços que criam formas distintas separadas serão contidas em unidades diferentes. Cada unidade contém informações detalhadas sobre seus traços de tinta, incluindo o objeto reconhecido, suas coordenadas e outros atributos de desenho.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Formas reconhecidas pela API do reconhecedor de tinta

A API do reconhecedor de tinta pode identificar as formas usadas com mais frequência no note. A imagem abaixo mostra alguns exemplos básicos. Para obter uma lista completa de formas e outros conteúdos de tinta reconhecidos pela API, consulte o [artigo de referência de API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![A lista de formas reconhecidas pela API do reconhecedor de tinta](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Padrões de chamada recomendados

Você pode chamar a API REST do reconhecedor de tinta em padrões diferentes de acordo com seu aplicativo. 

### <a name="user-initiated-api-calls"></a>Chamadas à API iniciadas pelo usuário

Se você estiver criando um aplicativo que usa a entrada do usuário (por exemplo, um aplicativo de anotação ou anotação), talvez queira dar a eles o controle de quando e qual tinta é enviada para a API do reconhecedor de tinta. Essa funcionalidade é especialmente útil quando o texto e as formas estão presentes na tela e os usuários desejam executar ações diferentes para cada um. Considere adicionar recursos de seleção (como um laço ou outra ferramenta de seleção geométrica) que permitem aos usuários escolher o que é enviado para a API.  

### <a name="app-initiated-api-calls"></a>Chamadas à API iniciadas pelo aplicativo

Você também pode fazer com que seu aplicativo chame a API do reconhecedor de tinta após um tempo limite. Ao enviar os traços de tinta atuais para a API rotineiramente, você pode armazenar os resultados de reconhecimento à medida que eles são criados ao melhorar o tempo de resposta da API. Por exemplo, você pode enviar uma linha de texto manuscrito para a API depois de detectar que o usuário a concluiu. 

Ter os resultados do reconhecimento antecipadamente fornece informações sobre as características dos traços de tinta conforme eles se relacionam entre si. Por exemplo, quais traços são agrupados para formar a mesma palavra, linha, lista, parágrafo ou forma. Essas informações podem aprimorar os recursos de seleção de tinta do seu aplicativo, sendo capazes de selecionar grupos de traços de uma só vez, por exemplo.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrar a API do reconhecedor de tinta com o Windows Ink

O [Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) fornece ferramentas e tecnologias para habilitar experiências de tinta digital em uma variedade diversificada de dispositivos. Você pode combinar a plataforma Windows Ink com a API de reconhecimento de tinta para criar aplicativos que exibem e interpretam traços de tinta digital.

## <a name="next-steps"></a>Passos Seguintes

* [O que é a API do reconhecedor de tinta?](../overview.md)
* [Referência da API REST do reconhecedor de tinta](https://go.microsoft.com/fwlink/?linkid=2089907)

* Comece a enviar dados de traços de tinta digital usando:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)