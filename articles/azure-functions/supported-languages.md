---
title: Línguas apoiadas em Funções Azure
description: Saiba quais as línguas suportadas (GA) e que estão em pré-visualização, e formas de alargar o desenvolvimento das Funções a outras línguas.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994906"
---
# <a name="supported-languages-in-azure-functions"></a>Línguas apoiadas em Funções Azure

Este artigo explica os níveis de suporte oferecidos para línguas que pode usar com funções Azure. Também descreve estratégias para criar funções usando línguas não apoiadas de forma nativa.

## <a name="levels-of-support"></a>Níveis de apoio

Existem dois níveis de apoio:

* **Geralmente disponível (GA)** - Totalmente suportado e aprovado para utilização da produção.
* **Pré-visualização** - Ainda não suportado, mas espera-se que atinja o estatuto de GA no futuro.

## <a name="languages-by-runtime-version"></a>Idiomas por versão runtime 

Estão disponíveis [três versões do tempo de funcionamento das Funções Azure.](functions-versions.md) A tabela que se segue mostra quais as línguas suportadas em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>Manipuladores personalizados (pré-visualização) 

Os manipuladores personalizados são servidores web leves que recebem eventos do anfitrião funções do Azure. Qualquer idioma que suporte os primitivos HTTP pode implementar um manipulador personalizado. Isto significa que os manipuladores personalizados podem ser usados para criar funções em idiomas que não são oficialmente suportados. Para saber mais, consulte [os manipuladores personalizados do Azure Functions (pré-visualização)](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Extibilidade linguística

A partir da versão 2.x, o tempo de execução foi concebido para oferecer [a extibilidade linguística.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) As línguas JavaScript e Java no tempo de execução 2.x são construídas com esta extensibility.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como desenvolver funções nas línguas apoiadas, consulte os seguintes recursos:

+ [Referência de desenvolvedor de biblioteca de classe C#](functions-dotnet-class-library.md)
+ [Referência c# script developer](functions-reference-csharp.md)
+ [Referência java desenvolvedor](functions-reference-java.md)
+ [Referência do desenvolvedor JavaScript](functions-reference-node.md)
+ [Referência do desenvolvedor PowerShell](functions-reference-powershell.md)
+ [Referência de desenvolvedor python](functions-reference-python.md)
+ [Referência do desenvolvedor typeScript](functions-reference-node.md#typescript)
