---
title: Línguas apoiadas em Funções Azure
description: Saiba quais as línguas suportadas (GA) e quais são experimentais ou em pré-visualização.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74942261"
---
# <a name="supported-languages-in-azure-functions"></a>Línguas apoiadas em Funções Azure

Este artigo explica os níveis de suporte oferecidos para línguas que pode usar com funções Azure.

## <a name="levels-of-support"></a>Níveis de apoio

Existem três níveis de apoio:

* **Geralmente disponível (GA)** - Totalmente suportado e aprovado para utilização da produção.
* **Pré-visualização** - Ainda não suportado, mas espera-se que atinja o estatuto de GA no futuro.
* **Experimental** - Não suportado e poderá ser abandonado no futuro; nenhuma garantia de pré-visualização eventual ou estatuto de GA.

## <a name="languages-by-runtime-version"></a>Idiomas por versão runtime 

Estão disponíveis [três versões do tempo de funcionamento das Funções Azure.](functions-versions.md) A tabela que se segue mostra quais as línguas suportadas em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Línguas experimentais

As línguas experimentais na versão 1.x não escalam bem e não suportam todas as encadernações.

Não use funcionalidades experimentais para nada em que confie, pois não há apoio oficial para eles. Os casos de apoio não devem ser abertos para problemas com línguas experimentais. 

Versões posteriores não suportam línguas experimentais. O apoio a novas línguas só é adicionado quando a língua pode ser apoiada na produção. 

### <a name="language-extensibility"></a>Extibilidade linguística

A partir da versão 2.x, o tempo de execução foi concebido para oferecer [a extibilidade linguística.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) As línguas JavaScript e Java no tempo de execução 2.x são construídas com esta extensibility.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como desenvolver funções nas línguas apoiadas, consulte os seguintes recursos:

+ [Referência de desenvolvedor de biblioteca de classe C#](functions-dotnet-class-library.md)
+ [Referência c# script developer](functions-reference-csharp.md)
+ [Referência java desenvolvedor](functions-reference-java.md)
+ [Referência do desenvolvedor JavaScript](functions-reference-node.md)
+ [Referência do desenvolvedor PowerShell](functions-reference-powershell.md)
+ [Referência de desenvolvedor python](functions-reference-python.md)
+ [Referência do desenvolvedor typeScript](functions-reference-node.md#typescript)
