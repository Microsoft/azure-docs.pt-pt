---
title: 'Quickstart: Criar e utilizar o ciclo de aprendizagem com a SDK - Personalizer'
description: Este quickstart mostra-lhe como criar e gerir a sua base de conhecimentos utilizando a biblioteca de clientes Personalizer.
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalização, personalizador Azure, machine learning
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: dccdec0888f2968fb7089c4ff80c9338215de135
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055997"
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

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como funciona](how-personalizer-works.md) 
>  o Personaler [Onde usar o Personalizar?](where-can-you-use-personalizer.md)
