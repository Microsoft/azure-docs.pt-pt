---
title: Idiomas com suporte no Azure Functions
description: Saiba quais idiomas têm suporte (GA) e quais são experimentais ou em versão prévia.
ms.topic: conceptual
ms.date: 08/02/2018
ms.openlocfilehash: 3b61dd83e481d42dd15f089247d016c6b71ff0de
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230288"
---
# <a name="supported-languages-in-azure-functions"></a>Idiomas com suporte no Azure Functions

Este artigo explica os níveis de suporte oferecidos para os idiomas que podem ser usados com Azure Functions.

## <a name="levels-of-support"></a>Níveis de suporte

Há três níveis de suporte:

* Disponibilidade **geral (GA)** – totalmente suportado e aprovado para uso em produção.
* **Visualização** -ainda não tem suporte, mas espera-se alcançar o status de GA no futuro.
* **Experimental** – sem suporte e pode ser abandonado no futuro; nenhuma garantia de status de GA ou de visualização eventual.

## <a name="languages-by-runtime-version"></a>Idiomas por versão de tempo de execução 

[Duas versões do tempo de execução do Azure Functions](functions-versions.md) estão disponíveis. A tabela a seguir mostra quais idiomas têm suporte em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Idiomas experimentais

As linguagens experimentais na versão 1. x não são bem dimensionadas e não dão suporte a todas as associações.

Não use recursos experimentais para qualquer coisa na qual você dependa, pois não há suporte oficial para eles. Os casos de suporte não devem ser abertos para problemas com linguagens experimentais. 

O tempo de execução da versão 2. x não dá suporte a idiomas experimentais. O suporte para novos idiomas é adicionado somente quando o idioma pode ter suporte na produção. 

### <a name="language-extensibility"></a>Extensibilidade da linguagem

A partir da versão 2. x, o tempo de execução foi projetado para oferecer [extensibilidade de linguagem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). As linguagens JavaScript e Java no tempo de execução 2. x são criadas com essa extensibilidade.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como desenvolver funções nos idiomas com suporte, consulte os seguintes recursos:

+ [C#referência do desenvolvedor da biblioteca de classes](functions-dotnet-class-library.md)
+ [C#referência do desenvolvedor de script](functions-reference-csharp.md)
+ [Referência do desenvolvedor Java](functions-reference-java.md)
+ [Referência do desenvolvedor de JavaScript](functions-reference-node.md)
+ [Referência do desenvolvedor do PowerShell](functions-reference-powershell.md)
+ [Referência do desenvolvedor do Python](functions-reference-python.md)
+ [Referência do desenvolvedor TypeScript](functions-reference-node.md#typescript)
