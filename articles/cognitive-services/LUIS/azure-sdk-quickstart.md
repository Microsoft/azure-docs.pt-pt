---
title: 'Quickstart: Language Understanding (LUIS) Bibliotecas de clientes SDK'
description: Crie e questione uma aplicação LUIS com as bibliotecas de clientes LUIS SDK com este quickstart usando C#, Python ou JavaScript.
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, inteligência artificial, ai, processamento de linguagem natural, nlp, compreensão da linguagem natural, nlu, ai conversa, ai conversador, ai chatbot, chatbot maker, LUIS, nlp ai, luis ai, azure luis, compreensão da linguagem natural
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323248"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>Quickstart: Language Understanding (LUIS) SDK bibliotecas de clientes para criar e consultar a sua app LUIS

Crie e questione uma aplicação LUIS com as bibliotecas de clientes LUIS SDK com este quickstart usando C#, Python ou JavaScript.

A Compreensão da Linguagem (LUIS) permite-lhe aplicar inteligência personalizada de aprendizagem automática ao texto de linguagem natural e conversadora de um utilizador para prever o significado geral e retirar informações relevantes e detalhadas.

* A biblioteca de clientes **SDK** de autor permite-lhe criar, editar, treinar e publicar a sua aplicação LUIS. * A biblioteca de clientes **SDK de previsão** permite-lhe consultar a aplicação publicada.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar a aplicação do [portal LUIS](https://www.luis.ai) e eliminar os recursos do Azure do [portal Azure.](https://portal.azure.com/)

## <a name="troubleshooting"></a>Resolução de problemas

* Autenticação na biblioteca do cliente - erros de autenticação geralmente indicam que a chave errada & ponto final foram usados. Este quickstart usa a chave de autoria e ponto final para o tempo de previsão, como uma conveniência, mas só funcionará se ainda não tiver usado a quota mensal. Se não puder utilizar a chave de autoria e o ponto final, tem de utilizar a chave de tempo de previsão e o ponto final ao aceder à biblioteca de clientes SDK em tempo de previsão.
* Criar entidades - se tiver um erro ao criar a entidade aninhada de aprendizagem automática utilizada neste tutorial, certifique-se de que copiou o código e não alterou o código para criar uma entidade diferente.
* Criando palavras de exemplo - se tiver um erro ao criar a expressão de exemplos escrita utilizada neste tutorial, certifique-se de que copiou o código e não alterou o código para criar um exemplo diferente.
* Formação - se tiver um erro de formação, isto geralmente indica uma aplicação vazia (sem intenções com palavras de exemplo), ou uma app com intenções ou entidades que estão mal formadas.
* Erros diversos - porque o código chama para as bibliotecas do cliente com texto e objetos JSON, certifique-se de que não alterou o código.

Outros erros - se tiver um erro não coberto na lista anterior, avise-nos dando feedback na parte inferior desta página. Inclua a linguagem de programação e a versão das bibliotecas cliente que instalou. 

## <a name="next-steps"></a>Próximos passos

* [O que é a API de Compreensão linguística (LUIS) ?](what-is-luis.md)
* [O que há de novo?](whats-new.md)
* [Intenções](luis-concept-intent.md), [entidades](luis-concept-entity-types.md), e [exemplos de declarações](luis-concept-utterance.md), e [entidades pré-construídas](luis-reference-prebuilt-entities.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
