---
title: 'Início rápido: criar e usar o loop de aprendizagem com o SDK-personalizador'
description: Este guia de início rápido mostra como criar e gerenciar sua base de dados de conhecimento usando o SDK do cliente.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: e09476bc084465cf08087a3200d8b7d663b0685e
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122909"
---
# <a name="quickstart-personalizer-client-library"></a>Início rápido: biblioteca de cliente do personalizador

Exiba o conteúdo personalizado neste guia de início rápido do Python com o serviço personalizado.

Introdução à biblioteca de cliente do personalizador. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

 * API de classificação – seleciona o melhor item, de itens de conteúdo, com base em informações em tempo real que você fornece sobre conteúdo e contexto.
 * Premiar a API-você determina a pontuação de recompensa com base nas suas necessidades de negócios e, em seguida, envia-a para o personalizador com essa API. Essa Pontuação pode ser um valor único, como 1 para bom, e 0 para ruim ou um algoritmo criado com base nas suas necessidades de negócios.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

::: zone pivot="programming-language-nodejs"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Como o personalizador funciona](how-personalizer-works.md)

* [O que é personalizador?](what-is-personalizer.md)
* [Onde você pode usar o personalizador?](where-can-you-use-personalizer.md)
* [Resolução de Problemas](troubleshooting.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
