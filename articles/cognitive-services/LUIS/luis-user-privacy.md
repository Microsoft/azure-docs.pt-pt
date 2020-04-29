---
title: Export& apagar dados - LUIS
titleSuffix: Azure Cognitive Services
description: Tem total controlo sobre visualização, exportação e aparas os seus dados. Elimine os dados do cliente para garantir a privacidade e conformidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273365"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e eliminar os dados dos seus clientes em Compreensão linguística (LUIS) em Serviços Cognitivos

Elimine os dados do cliente para garantir a privacidade e conformidade.

## <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados dos clientes
O Serviço Inteligente de Compreensão linguística (LUIS) preserva o conteúdo do cliente para operar o serviço, mas o utilizador da LUIS tem total controlo sobre visualização, exportação e apagamento dos seus dados. Isto pode ser feito através do [portal](luis-reference-regions.md) da LUIS ou da [Autoria LUIS (também conhecida como Programática) APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

O conteúdo do cliente é armazenado encriptado no armazenamento regional do Microsoft Azure e inclui:

- Conteúdo da conta de utilizador recolhido no registo
- Dados de formação necessários para construir os modelos
- Consultas de utilizadores registadas usadas pela [aprendizagem ativa](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os utilizadores podem desativar `&log=false` a consulta de registo através da aplicação do pedido, detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Apagar dados dos clientes
Os utilizadores da LUIS têm controlo total para eliminar qualquer conteúdo do utilizador, seja através do portal web LUIS ou da LUIS Authoring (também conhecida como Programática) APIs. A tabela seguinte apresenta links de assistência com ambos:

| | **Conta de Utilizador** | **Aplicação** | **Exemplo de expressão(s)** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Ligação](luis-concept-data-storage.md#delete-an-account) | [Ligação](luis-how-to-start-new-app.md#delete-app) | [Ligação](luis-concept-data-storage.md#utterances-in-an-intent) | [Expressões de aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Expressões registadas](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportação de dados dos clientes
Os utilizadores da LUIS têm controlo total para visualizar os dados no portal, no entanto devem ser exportados através das APIs de Autor (também conhecidas como Programáticas). A tabela seguinte apresenta links de assistência à exportação de dados através da AUTORÇÃO LUIS (também conhecida como Programática) APIs:

| | **Conta de Utilizador** | **Aplicação** | **Proclamação(s)** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Localização da aprendizagem ativa

Para permitir a [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#enable-active-learning), as declarações registadas dos utilizadores, recebidas nos pontos finais do LUIS publicados, são armazenadas nas seguintes geografias Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [E.U.A.](#united-states)

Com exceção dos dados de aprendizagem ativa (detalhados abaixo), a LUIS segue as práticas de armazenamento de [dados dos serviços regionais.](https://azuredatacentermap.azurewebsites.net/)

### <a name="europe"></a>Europa

O portal [eu.luis.ai](https://eu.luis.ai) e a Europe Authoring (também conhecido como APIs Programáticos) estão hospedados na geografia europeia de Azure. O portal eu.luis.ai e a Europe Authoring (também conhecidocomo APIs Programático) apoiam a implantação de pontos finais para as seguintes geografias Azure:

* Europa
* França
* Reino Unido

Ao implantar estas geografias Azure, as declarações recebidas pelo ponto final dos utilizadores finais da sua app serão armazenadas na geografia europeia do Azure para aprendizagem ativa. Pode desativar a aprendizagem ativa, ver [Desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Para gerir as expressões armazenadas, consulte a [expressão Delete](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Austrália

O portal [au.luis.ai](https://au.luis.ai) e a Austrália Authoring (também conhecidos como APIs Programáticos) estão hospedados na geografia australiana de Azure. O portal au.luis.ai e a Austrália Authoring (também conhecidos como APIs Programáticos) apoiam a implantação de pontos finais para as seguintes geografias Azure:

* Austrália

Ao implantar estas geografias Azure, as declarações recebidas pelo ponto final dos utilizadores finais da sua app serão armazenadas na geografia australiana do Azure para aprendizagem ativa. Pode desativar a aprendizagem ativa, ver [Desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Para gerir as expressões armazenadas, consulte a [expressão Delete](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Estados Unidos

O portal [luis.ai](https://www.luis.ai) e a Autoria dos Estados Unidos (também conhecidas como APIs Programáticas) estão hospedados na geografia dos Estados Unidos do Azure. O portal luis.ai e a Autoria dos Estados Unidos (também conhecidas como APIs Programáticas) apoiam a implantação de pontos finais para as seguintes geografias Azure:

* Geografias azure não apoiadas pelas regiões autorais europa ou austrália

Ao implantar estas geografias Azure, as declarações recebidas pelo ponto final dos utilizadores finais da sua app serão armazenadas na geografia dos Estados Unidos da Azure para aprendizagem ativa. Pode desativar a aprendizagem ativa, ver [Desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Para gerir as expressões armazenadas, consulte a [expressão Delete](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência das regiões LUIS](./luis-reference-regions.md)
