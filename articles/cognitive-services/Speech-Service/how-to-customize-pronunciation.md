---
title: Personalizar pronúncia - serviços de voz
titlesuffix: Azure Cognitive Services
description: Saiba como personalizar pronúncia com o serviço de voz. Com pronúncia personalizada, pode definir o formulário fonético e a exibição de uma palavra ou o termo. É útil para lidar com os termos personalizados, tais como nomes de produto ou acrônimos. Tudo o que precisa para começar a utilizar é um ficheiro de pronúncia – um arquivo. txt simples.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 139c5d47fe6ea82148e2d5e1cf2f5fcb72d4020e
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339402"
---
# <a name="enable-custom-pronunciation"></a>Ativar pronúncia personalizada

Ao utilizar pronúncia personalizada, pode definir o formulário fonético e a exibição de uma palavra ou o termo (acrônimo). É útil para lidar com os termos personalizados, tais como nomes de produto ou acrônimos. Tudo o que precisa para começar a utilizar é um ficheiro de pronúncia – um arquivo. txt simples.

Eis como funciona. Num arquivo. txt único, pode inserir várias entradas de pronúncia personalizado. A estrutura é o seguinte:

```
Display form <Tab> Spoken form <Newline>
```

Vários exemplos são mostrados na tabela a seguir:

| Formulário de apresentação | Formulário falado |
|----------|-------|
| 3CPO | Veja três pea s |
| L8R | são mais tarde |
| CNTK | k de n de t c|

## <a name="requirements-for-the-spoken-form"></a>Requisitos para o formulário falado

O formulário falado tem de estar em minúsculas, que pode forçar durante a importação. Também tem de fornecer verificações no importador de dados. Nenhum separador no formulário falado ou o formulário de apresentação é permitida. No entanto, existem poderá ser mais proibido carateres no formulário de apresentação (por exemplo, ~ e ^).

Cada arquivo. txt pode ter várias entradas, conforme mostrado na imagem seguinte:

![Exemplos de pronúncia de acrônimos](media/stt/custom-speech-pronunciation-file.png)

O formulário falado é a sequência de fonética do formulário de apresentação. Ele é composto por letras, palavras ou sílabas. Atualmente, não existe mais orientações ou conjunto de padrões para o ajudar a formular a forma falada.

## <a name="supported-pronunciation-characters"></a>Pronúncia suportados carateres

Pronúncia personalizada é atualmente suportada para inglês (en-US) e alemão (Alemanha-de). Os conjuntos de carateres que pode utilizar para expressar o formulário falado de um período (no arquivo pronúncia personalizado) são mostrados na tabela a seguir:

| Idioma | Carateres |
|---------- |----------|
| Inglês (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão (Alemanha-de) | ä, ö, ü, ?, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> Formulário de apresentação de um termo (num arquivo de pronúncia) deve ser escrito da mesma forma num conjunto de dados de adaptação de idioma.

## <a name="requirements-for-the-display-form"></a>Requisitos para o formulário de apresentação

Um formulário de apresentação pode ser apenas uma palavra personalizada, um acrônimo ou palavras compostas que combinam palavras existentes.

>[!NOTE]
>Não é recomendado utilizar esta funcionalidade para reformulate palavras comuns ou para modificar o formato falado. É melhor executar o Decodificador para ver se algumas palavras pouco habitual (por exemplo, abreviaturas, palavras técnicas ou palavras estrangeiras) estão incorretamente decodificadas. Se forem, adicioná-los para o ficheiro de pronúncia personalizado. No modelo de idioma, deve sempre e apenas usar o formulário de apresentação de uma palavra.

## <a name="requirements-for-the-file-size"></a>Requisitos para o tamanho do ficheiro
O tamanho do ficheiro. txt que contenha as entradas de pronúncia está limitado a 1 MB (1KB para chaves de escalão gratuito). Normalmente, não precisa de carregar grandes quantidades de dados por meio deste ficheiro. A maioria dos arquivos de pronúncia personalizados são probabilidade de serem apenas alguns quilobytes (KBs) de tamanho. A codificação do ficheiro. txt para todas as localidades deve ser o UTF-8 BOM. Para a Localidade do inglês, ANSI também é aceitável.

## <a name="next-steps"></a>Passos Seguintes
* Melhorar a precisão de reconhecimento através da criação de um [um modelo acústico personalizado](how-to-customize-acoustic-models.md).
* Melhorar a precisão de reconhecimento através da criação de um [modelo de idioma personalizado](how-to-customize-language-model.md).
