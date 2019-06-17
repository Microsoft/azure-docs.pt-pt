---
title: Idiomas suportados nas funções do Azure
description: Saiba que idiomas são suportados (GA) e que são experimental ou em pré-visualização.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148833"
---
# <a name="supported-languages-in-azure-functions"></a>Idiomas suportados nas funções do Azure

Este artigo explica que os níveis de suporte oferecido para idiomas que pode usar com as funções do Azure.

## <a name="levels-of-support"></a>Níveis de suporte

Existem três níveis de suporte:

* **Disponibilidade geral (GA)** - totalmente suportado e aprovada para utilização em produção.
* **Pré-visualização** - ainda não suportado, mas é esperado para alcançar o estado de disponibilidade geral no futuro.
* **Experimental** – não suportado e pode ser abandonada no futuro; nenhuma garantia de pré-visualização eventual ou estado de disponibilidade geral.

## <a name="languages-in-runtime-1x-and-2x"></a>Linguagens no runtime 1.x e 2.x

[Duas versões do runtime das funções do Azure](functions-versions.md) estão disponíveis. A tabela seguinte mostra que idiomas são suportados em cada versão de runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguagens experimentais

As linguagens experimentais na versão 1.x não dimensiona bem e não oferecem suporte a todas as ligações.

Não utilize funcionalidades experimentais para tudo o que contar, porque não existe nenhum suporte oficial para eles. Incidentes de suporte não devem ser abertos para problemas com linguagens experimentais. 

A versão 2.x do runtime não suporta linguagens experimentais. Suporte para novos idiomas é adicionado apenas quando o idioma pode ser suportado em produção. 

### <a name="language-extensibility"></a>Extensibilidade de idioma

O runtime 2.x é projetado para oferecer [extensibilidade da linguagem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Os idiomas de JavaScript e Java no 2.x runtime são criados com essa extensibilidade.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como utilizar uma das linguagens de disponibilidade geral ou de pré-visualização nas funções do Azure, consulte os seguintes recursos:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
