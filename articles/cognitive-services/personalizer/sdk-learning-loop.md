---
title: 'Quickstart: Criar e utilizar o ciclo de aprendizagem com a SDK - Personalizer'
description: Este quickstart mostra-lhe como criar e gerir a sua base de conhecimentos utilizando o cliente SDK.
ms.topic: quickstart
ms.date: 07/30/2020
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 466146f6876feb9d0a84be85c2ddeaebb17ac2ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928071"
---
# <a name="quickstart-personalizer-client-library"></a>Quickstart: Biblioteca de clientes personalizado

Exiba conteúdo personalizado neste quickstart com o serviço Personalizar.

Começa com a biblioteca de clientes Personalizer. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

 * Rank API - Seleciona o melhor item, a partir de ações, com base em informações em tempo real que fornece sobre conteúdo e contexto.
 * Recompensa API - Você determina a pontuação da recompensa com base nas necessidades do seu negócio e, em seguida, envie-a para Personalizer com esta API. Essa pontuação pode ser um único valor, como 1 para o bem, e 0 para o mal, ou um algoritmo que crias com base nas necessidades do teu negócio.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Como funciona o Personalizador](how-personalizer-works.md)

* [O que é o Personalizador?](what-is-personalizer.md)
* [Onde pode utilizar o Personalizador?](where-can-you-use-personalizer.md)
* [Resolução de problemas](troubleshooting.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer).
