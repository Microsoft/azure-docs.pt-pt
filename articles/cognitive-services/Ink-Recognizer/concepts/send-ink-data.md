---
title: Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital
titleSuffix: Azure Cognitive Services
description: Saiba ligar para a API do Analisador de Tinta para diferentes aplicações
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221108"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Enviar dados de tinta digital para a API do Reconhecedor de Tinta Digital 

Tinta digital refere-se às tecnologias que permitem representações digitais de entrada, como escrita manual e desenhos. Isto é tipicamente conseguido usando um digitalizador que captura os movimentos de dispositivos de entrada, como um estiluição. À medida que os dispositivos continuam a permitir experiências avançadas de tinta digital, a inteligência artificial e a aprendizagem automática permitem o reconhecimento de texto e formas escritas em qualquer contexto. A API do Reconhecimento de Tinta permite-lhe enviar tacadas de tinta e obter informações detalhadas sobre eles. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Os serviços de Reconhecimento de Tinta API vs. OCR

A API do reconhecimento de tinta não utiliza reconhecimento ótico de caracteres (OCR). Os serviços oCR processam os dados de pixel a partir de imagens para fornecer caligrafia e reconhecimento de texto. Isto às vezes é chamado reconhecimento offline. Em vez disso, a API do Reconhecimento de Tinta requer dados digitais de traçado de tinta que são capturados à medida que o dispositivo de entrada é usado. O processamento de dados de tinta digital desta forma pode produzir resultados de reconhecimento mais precisos em comparação com os serviços OCR. 

## <a name="sending-ink-data"></a>Envio de dados de tinta

A API do Reconhecimento de Tinta requer as coordenadas X e Y que representam os traços de tinta criados por um dispositivo de entrada, desde o momento em que toca na superfície de deteção até ao momento em que é levantada. Os pontos de cada traçado devem ser uma série de valores separados de vírem, e ser formatados em JSON como o exemplo abaixo. Além disso, cada tacada de tinta deve ter uma identificação única em cada pedido. Se o ID for repetido dentro do mesmo pedido, a API devolverá um erro. Para obter os resultados de reconhecimento mais precisos, tenha pelo menos oito dígitos após o ponto decimal. A origem (0,0) da tela é assumida como o canto superior esquerdo da tela de tinta.

> [!NOTE]
> O exemplo que se segue não é válido jSON. Você pode encontrar um pedido json reconhecível de tinta completo no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

A API do Reconhecimento de Tinta devolve uma resposta de análise sobre os objetos que reconheceu a partir do conteúdo da tinta. A resposta contém unidades de reconhecimento que descrevem as relações entre diferentes tacadas de tinta. Por exemplo, os derrames que criam formas distintas e separadas serão contidos em diferentes unidades. Cada unidade contém informações detalhadas sobre os seus traços de tinta, incluindo o objeto reconhecido, as suas coordenadas e outros atributos de desenho.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Formas reconhecidas pela API do Reconhecimento de Tinta

A API do Reconhecimento de Tinta pode identificar as formas mais usadas na tomada de nota. A imagem abaixo mostra alguns exemplos básicos. Para obter uma lista completa de formas e outros conteúdos de tinta reconhecidos pela [API,](https://go.microsoft.com/fwlink/?linkid=2089907)consulte o artigo de referência da API . 

![A lista de formas reconhecidas pela API do Reconhecimento de Tinta](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Padrões de chamada recomendados

Pode chamar a API REST Reconheço-tinta em diferentes padrões de acordo com a sua aplicação. 

### <a name="user-initiated-api-calls"></a>User iniciou chamadas API

Se estiver a construir uma aplicação que leve a entrada do utilizador (por exemplo, uma aplicação de tomada de nota ou de anotação), poderá querer dar-lhes o controlo de quando e qual a tinta enviada para a API do Reconhecimento de Tinta. Esta funcionalidade é especialmente útil quando texto e formas estão ambos presentes na tela, e os utilizadores querem realizar diferentes ações para cada um. Considere adicionar funcionalidades de seleção (como uma ferramenta de seleção de lasso ou outra geométrica) que permitem aos utilizadores escolher o que é enviado para a API.  

### <a name="app-initiated-api-calls"></a>App iniciou chamadas API

Também pode ter a sua aplicação chamada API do Reconhecimento de Tinta após um intervalo. Ao enviar os atuais traços de tinta para a API rotineiramente, pode armazenar os resultados do reconhecimento à medida que são criados, melhorando o tempo de resposta da API. Por exemplo, pode enviar uma linha de texto manuscrito para a API depois de detetar que o seu utilizador o completou. 

Ter o reconhecimento resulta antecipadamente dá-lhe informações sobre as características dos traços de tinta à medida que se relacionam uns com os outros. Por exemplo, quais os traçados que são agrupados para formar a mesma palavra, linha, lista, parágrafo ou forma. Estas informações podem melhorar as funcionalidades de seleção de tinta da sua aplicação, podendo selecionar grupos de traçados de uma só vez, por exemplo.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrar a API do Reconhecimento de Tinta com tinta windows

[O Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) fornece ferramentas e tecnologias para permitir experiências de tinta digital numa gama diversificada de dispositivos. Pode combinar a plataforma Windows Ink com a API do Reconhecimento de Tinta para criar aplicações que apresentem e interpretem traços de tinta digital.

## <a name="next-steps"></a>Passos seguintes

* [O que é a API do Reconhecedor de Tinta Digital?](../overview.md)
* [Referência da API do Reconhecimento de Tinta](https://go.microsoft.com/fwlink/?linkid=2089907)

* Comece a enviar dados digitais de traçado de tinta usando:
    * [C #](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
