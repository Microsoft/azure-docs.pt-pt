---
title: Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre chamar a API do Analisador de Tinta para diferentes aplicações
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 8665e27b6b7c43bce7d749d9bfe0a2f815a0f46d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051105"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Tinta digital refere-se às tecnologias que permitem representações digitais de entrada, como escrita manual e desenhos. Isto é tipicamente conseguido usando um digitalizador que captura os movimentos dos dispositivos de entrada, como um estilete. À medida que os dispositivos continuam a permitir experiências avançadas de tinta digital, a inteligência artificial e a aprendizagem automática permitem o reconhecimento de texto e formas escritas em qualquer contexto. A API do Reconhecimento de Tinta permite-lhe enviar traços de tinta e obter informações detalhadas sobre eles. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Os serviços de reconhecimento de tinta API vs. OCR

A API do Reconhecimento de Tintas não utiliza reconhecimento de caracteres óticos (OCR). Os serviços OCR processam os dados do pixel a partir de imagens para fornecer caligrafia e reconhecimento de texto. Isto às vezes é chamado de reconhecimento offline. Em vez disso, a API do Reconhecimento de Tinta requer dados digitais de traçado de tinta que são capturados à medida que o dispositivo de entrada é usado. O processamento de dados digitais de tinta desta forma pode produzir resultados de reconhecimento mais precisos em comparação com os serviços OCR. 

## <a name="sending-ink-data"></a>Envio de dados de tinta

A API do Reconhecimento de Tinta requer as coordenadas X e Y que representam os traços de tinta criados por um dispositivo de entrada, desde o momento em que toca na superfície de deteção até ao momento em que é levantado. Os pontos de cada traçado devem ser uma cadeia de valores separados de vírgula, e ser formatados em JSON como o exemplo abaixo. Além disso, cada traçado de tinta deve ter uma identificação única em cada pedido. Se o ID for repetido dentro do mesmo pedido, a API devolverá um erro. Para obter os resultados de reconhecimento mais precisos, tenha pelo menos oito dígitos após o ponto decimal. Presume-se que a origem (0,0) da tela seja o canto superior esquerdo da tela de tinta.

> [!NOTE]
> O exemplo a seguir não é válido JSON. Você pode encontrar um pedido completo de Tinta Reconhecíver JSON no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Resposta do reconhecimento de tinta

A API do Reconhecimento de Tinta devolve uma resposta de análise sobre os objetos que reconheceu a partir do conteúdo da tinta. A resposta contém unidades de reconhecimento que descrevem as relações entre diferentes traços de tinta. Por exemplo, os traços que criam formas distintas e separadas serão contidos em diferentes unidades. Cada unidade contém informações detalhadas sobre os seus traços de tinta, incluindo o objeto reconhecido, as suas coordenadas e outros atributos de desenho.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Formas reconhecidas pela API do Reconhecimento de Tinta

A API do Reconhecimento de Tintas pode identificar as formas mais usadas na tomada de notas. A imagem abaixo mostra alguns exemplos básicos. Para obter uma lista completa de formas e outros conteúdos de tinta reconhecidos pela API, consulte o artigo de referência da [API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![A lista de formas reconhecidas pela API do Reconhecimento de Tinta](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Padrões de chamada recomendados

Pode ligar para a API do Reconhecimento de Tinta em diferentes padrões de acordo com a sua aplicação. 

### <a name="user-initiated-api-calls"></a>Utilizador iniciou chamadas de API

Se estiver a construir uma aplicação que leva a entrada do utilizador (por exemplo, uma aplicação de tomada de notas ou anotação), pode querer dar-lhes o controlo de quando e que tinta é enviada para a API do Reconhecimento de Tinta. Esta funcionalidade é especialmente útil quando o texto e as formas estão presentes na tela, e os utilizadores querem realizar diferentes ações para cada um. Considere adicionar funcionalidades de seleção (como um laço ou outra ferramenta de seleção geométrica) que permitam aos utilizadores escolher o que é enviado para a API.  

### <a name="app-initiated-api-calls"></a>App iniciou chamadas de API

Também pode pedir à sua aplicação para ligar para a API do Reconhecimento de Tinta após um intervalo. Ao enviar os cursos de tinta atuais para a API rotineiramente, você pode armazenar os resultados de reconhecimento à medida que são criados, melhorando o tempo de resposta da API. Por exemplo, pode enviar uma linha de texto manuscrito à API depois de ter detetado que o utilizador o completou. 

Ter os resultados de reconhecimento antecipadamente dá-lhe informações sobre as características dos traços de tinta que se relacionam entre si. Por exemplo, quais os traços agrupados para formar a mesma palavra, linha, lista, parágrafo ou forma. Estas informações podem melhorar as funcionalidades de seleção de tinta da sua aplicação, podendo selecionar grupos de traçados de uma só vez, por exemplo.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integre a API do Reconhecimento de Tinta com tinta do Windows

[O Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) fornece ferramentas e tecnologias para permitir experiências de tinta digital numa gama diversificada de dispositivos. Pode combinar a plataforma de Tinta Do Windows com a API do Reconhecimento de Tinta para criar aplicações que exibem e interpretam traços de tinta digital.

## <a name="next-steps"></a>Passos seguintes

* [O que é a API do Reconhecedor de Tinta Digital?](../overview.md)
* [Referência API do Reconhecimento de Tinta REST](https://go.microsoft.com/fwlink/?linkid=2089907)

* Comece a enviar dados digitais de traçado de tinta utilizando:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
