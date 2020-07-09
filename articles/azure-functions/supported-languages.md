---
title: Línguas apoiadas em Funções Azure
description: Saiba quais as línguas apoiadas (GA) e que estão em pré-visualização, e formas de estender o desenvolvimento de Funções a outras línguas.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994906"
---
# <a name="supported-languages-in-azure-functions"></a>Línguas apoiadas em Funções Azure

Este artigo explica os níveis de suporte oferecidos para as línguas que pode utilizar com Funções Azure. Também descreve estratégias para criar funções usando línguas não apoiadas de forma nativa.

## <a name="levels-of-support"></a>Níveis de apoio

Existem dois níveis de apoio:

* **Geralmente disponível (GA)** - Totalmente suportado e aprovado para utilização na produção.
* **Pré-visualização** - Ainda não suportado, mas espera-se que atinja o estatuto de GA no futuro.

## <a name="languages-by-runtime-version"></a>Línguas por versão runtime 

Estão disponíveis três [versões do tempo de funcionamento das Funções Azure.](functions-versions.md) A tabela a seguir mostra quais os idiomas suportados em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>Manipuladores personalizados (pré-visualização) 

Os manipuladores personalizados são servidores web leves que recebem eventos do anfitrião Azure Functions. Qualquer idioma que suporte primitivos HTTP pode implementar um manipulador personalizado. Isto significa que os manipuladores personalizados podem ser usados para criar funções em idiomas que não são oficialmente suportados. Para saber mais, consulte [os manipuladores personalizados Azure Functions (pré-visualização)](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Extensibilidade linguística

Começando pela versão 2.x, o tempo de execução foi concebido para oferecer [extensibilidade linguística.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) As línguas JavaScript e Java no tempo de execução de 2.x são construídas com esta extensibilidade.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como desenvolver funções nas línguas apoiadas, consulte os seguintes recursos:

+ [Referência de desenvolvedor de biblioteca de classe C#](functions-dotnet-class-library.md)
+ [C# referência do desenvolvedor de scripts](functions-reference-csharp.md)
+ [Referência do desenvolvedor de Java](functions-reference-java.md)
+ [Referência do desenvolvedor JavaScript](functions-reference-node.md)
+ [Referência do desenvolvedor PowerShell](functions-reference-powershell.md)
+ [Referência do desenvolvedor de Python](functions-reference-python.md)
+ [Referência do programador TypeScript](functions-reference-node.md#typescript)
