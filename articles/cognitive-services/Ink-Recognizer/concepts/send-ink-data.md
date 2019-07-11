---
title: Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital
titleSuffix: Azure Cognitive Services
description: Aprenda a chamar a API do analisador de tinta para diferentes aplicações
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 23431a6171f9ce4d2550ee62ac84679ce36126de
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721782"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital 

Tinta digital refere-se às tecnologias que permitem representações digitais de entrada, como escrita manual e desenhos. Normalmente, isto é conseguido com um digitalizador que captura os movimentos de dispositivos de entrada, como uma caneta. À medida que os dispositivos continuam a permitir experiências avançadas de tinta digital, a inteligência artificial e a aprendizagem automática permitem o reconhecimento de texto e formas escritas em qualquer contexto. A API do reconhecedor de tinta permite-lhe enviar traços de tinta e obter informações detalhadas sobre os mesmos. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Os vs do reconhecedor API de tinta. Serviços de OCR

A API do reconhecedor de tinta não utiliza óptica Recognition(OCR) de caráter. Serviços de OCR processam os dados de pixel de imagens para fornecer o reconhecimento de manuscrito e texto. Por vezes, isso é chamado reconhecimento offline. Em vez disso, a API do reconhecedor de tinta requer que os dados de traços de tinta digital são capturados como o dispositivo de entrada é utilizado. Processamento de dados de tinta digital desta forma pode produzir resultados de reconhecimento mais precisos em comparação comparados serviços de OCR. 

## <a name="sending-ink-data"></a>Enviar dados de tinta

A API do reconhecedor de tinta requer as coordenadas X e Y que representam os traços de tinta criados por um dispositivo de entrada, desde o momento que ele contempla a superfície de deteção quando é elevada. Os pontos de cada traço tem de ser uma cadeia de valores separados por vírgulas e ser formatados em JSON semelhante ao exemplo abaixo. Além disso, cada traço de tinta tem de ter um ID exclusivo em cada pedido. Se o ID é repetido dentro do mesmo pedido, a API irá devolver um erro. Para obter os resultados de reconhecimento mais precisos, ter, pelo menos, oito dígitos depois da casa decimal. A origem (0,0) da tela é considerada como o canto superior esquerdo da tela de escrita a tinta.

> [!NOTE]
> O exemplo a seguir não é um JSON válido. Pode encontrar um pedido de tinta reconhecedor JSON completo sobre [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

A API do reconhecedor de tinta devolve uma resposta de análise sobre os objetos reconheceu a partir do conteúdo de tinta. A resposta contém as unidades de reconhecimento que descrevem as relações entre os traços de tinta diferentes. Por exemplo, os traços que criar formas distintas, separadas estará contidos em unidades diferentes. Cada unidade contém informações detalhadas sobre os traços de tinta, incluindo o objeto reconhecido, suas coordenadas e outros atributos de desenho.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Formas reconhecidas pela API do reconhecedor de tinta

A API do reconhecedor de tinta pode identificar as formas mais usadas em anotações de nota. O abaixo imagem mostra alguns exemplos básicos. Para obter uma lista completa de formas e outra conteúda de tinta reconhecido pela API de mensagens em fila, consulte a [artigo de referência de API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![A lista de formas reconhecido pela API do reconhecedor de tinta](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Padrões de chamada recomendadas

Pode chamar a API de REST do reconhecedor de tinta em padrões diferentes, de acordo com seu aplicativo. 

### <a name="user-initiated-api-calls"></a>Chamadas de API iniciada pelo utilizador

Se estiver a criar uma aplicação que aceita entradas de utilizador (por exemplo, uma nota e tirar ou anotação aplicação), talvez queira dar-lhes controle de quando e qual tinta é enviada para a API do reconhecedor de tinta. Esta funcionalidade é especialmente útil quando o texto e formas são ambos presentes na tela e os usuários querem executar ações diferentes para cada um. Considere adicionar recursos de seleção (como um lasso ou outra ferramenta de seleção geométricos), que permitem aos utilizadores escolher o que é enviado para a API.  

### <a name="app-initiated-api-calls"></a>Aplicação iniciada chamadas de API

Também pode ter sua chamada de aplicação da API do reconhecedor de tinta após um tempo limite. Ao enviar regularmente os traços de tinta atual para a API, pode armazenar os resultados de reconhecimento à medida que forem criadas ao mesmo tempo melhorando o tempo de resposta da API. Por exemplo, pode enviar uma linha de texto manuscrito para a API depois de detetar o utilizador concluiu. 

Com que os resultados de reconhecimento com antecedência dá-lhe informações sobre as características de traços de tinta como se relacionam entre si. Por exemplo, os traços são agrupados para formar a mesma palavra, linha, lista, parágrafo ou forma. Esta informação pode melhorar a recursos de seleção de tinta da sua aplicação, a capacidade de selecionar grupos de traços ao mesmo tempo, por exemplo.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrar o reconhecedor de tinta API com o Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) fornece ferramentas e tecnologias para permitir experiências de escrita a tinta digital numa grande variedade de dispositivos. Pode combinar a plataforma de tinta do Windows com a API de reconhecimento de tinta para criar aplicativos que exibem e interpretam os traços de tinta digital.

## <a name="next-steps"></a>Passos seguintes

* [O que é a API do reconhecedor de tinta?](../overview.md)
* [Referência da API de REST do reconhecedor de tinta](https://go.microsoft.com/fwlink/?linkid=2089907)

* Começar a enviar dados de traços de tinta digital com:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)