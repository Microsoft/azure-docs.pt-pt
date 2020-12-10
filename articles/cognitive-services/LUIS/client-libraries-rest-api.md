---
title: 'Quickstart: Language Understanding (LUIS) Bibliotecas de clientes SDK e REST API'
description: Criar e consultar uma aplicação LUIS com as bibliotecas de clientes LUIS SDK e REST API.
ms.topic: quickstart
ms.date: 12/09/2020
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, inteligência artificial, ai, processamento de linguagem natural, nlp, LUIS, azure luis, compreensão da linguagem natural, ai chatbot, chatbot maker, compreensão da linguagem natural
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: 7ff5844cf9f1bce45df438fc00e24da28c438b05
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939220"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Quickstart: Bibliotecas de clientes de compreensão linguística (LUIS) e REST API

Crie e questione uma aplicação de inteligência artificial Azure LUIS (IA) com as bibliotecas de clientes LUIS SDK com este quickstart usando C#, Python ou JavaScript. Também pode utilizar o cURL para enviar pedidos utilizando a API REST.

A Compreensão da Linguagem (LUIS) permite-lhe aplicar o processamento de linguagem natural (NLP) ao texto de linguagem natural e conversacional de um utilizador para prever o significado geral e retirar informações relevantes e detalhadas.

* A biblioteca de clientes **autorais** e a API REST permitem-lhe criar, editar, treinar e publicar a sua app LUIS.
* A biblioteca de clientes **de previsão** e a API REST permitem-lhe consultar a aplicação publicada.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar a aplicação do [portal LUIS](https://www.luis.ai) e eliminar os recursos do Azure do [portal Azure.](https://portal.azure.com/)

Se estiver a utilizar a API REST, elimine o `ExampleUtterances.JSON` ficheiro do sistema de ficheiros quando terminar com o arranque rápido.

## <a name="troubleshooting"></a>Resolução de problemas

* Autenticação na biblioteca do cliente - erros de autenticação geralmente indicam que a chave errada & ponto final foram usados. Este quickstart usa a chave de autoria e ponto final para o tempo de previsão como uma conveniência, mas só funcionará se ainda não tiver usado a quota mensal. Se não puder utilizar a chave de autoria e o ponto final, tem de utilizar a chave de tempo de previsão e o ponto final ao aceder à biblioteca de clientes SDK em tempo de previsão.
* Criar entidades - se tiver um erro ao criar a entidade aninhada de aprendizagem automática utilizada neste tutorial, certifique-se de que copiou o código e não alterou o código para criar uma entidade diferente.
* Criando palavras de exemplo - se tiver um erro ao criar a expressão de exemplos escrita utilizada neste tutorial, certifique-se de que copiou o código e não alterou o código para criar um exemplo diferente.
* Formação - se tiver um erro de formação, isto geralmente indica uma aplicação vazia (sem intenções com palavras de exemplo), ou uma app com intenções ou entidades que estão mal formadas.
* Erros diversos - porque o código chama para as bibliotecas do cliente com texto e objetos JSON, certifique-se de que não alterou o código.

Outros erros - se tiver um erro não coberto na lista anterior, avise-nos dando feedback na parte inferior desta página. Inclua a linguagem de programação e a versão das bibliotecas cliente que instalou.

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Compreensão linguística (LUIS) ?](what-is-luis.md)
* [O que há de novo?](whats-new.md)
* [Intenções](luis-concept-intent.md), [entidades](luis-concept-entity-types.md), e [exemplos de declarações](luis-concept-utterance.md), e [entidades pré-construídas](luis-reference-prebuilt-entities.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
* Compreensão da linguagem natural: [compreensão da linguagem natural (NLU) e processamento de linguagem natural (NLP)](artificial-intelligence.md)
* Bots: [Chatbots da IA](luis-csharp-tutorial-bf-v4.md "tutorial de criador de chatbot")
