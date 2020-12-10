---
title: Exportação & apagar dados - LUIS
titleSuffix: Azure Cognitive Services
description: Tens total controlo sobre visualização, exportação e eliminação dos seus dados. Elimine os dados do cliente para garantir a privacidade e a conformidade.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/08/2020
ms.openlocfilehash: cd9b353300b88c69efb0c458245a86963c579064
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007743"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exporte e elimine os dados dos seus clientes em Compreensão de Linguagem (LUIS) em Serviços Cognitivos

Elimine os dados do cliente para garantir a privacidade e a conformidade.

## <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados de clientes
O Serviço Inteligente de Compreensão de Línguas (LUIS) preserva o conteúdo do cliente para operar o serviço, mas o utilizador LUIS tem total controlo sobre a visualização, exportação e eliminação dos seus dados. Isto pode ser feito através do [portal](luis-reference-regions.md) web LUIS ou da [AUTORia LUIS (também conhecida como Programática) APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

O conteúdo do cliente é armazenado encriptado no armazenamento regional da Microsoft Ezure e inclui:

- Conteúdo da conta de utilizador recolhido no registo
- Dados de formação necessários para construir os modelos
- Consultas de utilizador registadas usadas pela [aprendizagem ativa](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os utilizadores podem desativar a consulta de início de sessão por `&log=false` anexação ao pedido, detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes
Os utilizadores do LUIS têm total controlo para eliminar qualquer conteúdo do utilizador, seja através do portal web LUIS ou das APIs de Autoria LUIS (também conhecidas como Programáticas). A tabela a seguir apresenta ligações ajudando com ambos:

| | **Conta de Utilizador** | **Aplicação** | **Exemplos de declarações** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Ligação](luis-concept-data-storage.md#delete-an-account) | [Ligação](luis-how-to-start-new-app.md#delete-app) | [Ligação](luis-concept-data-storage.md#utterances-in-an-intent) | [Expressões de aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Declarações registadas](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportação de dados dos clientes
Os utilizadores do LUIS têm total controlo para visualizar os dados no portal, no entanto devem ser exportados através das APIs de Autoria LUIS (também conhecidas como Programáticas). A tabela a seguir apresenta ligações que ajudam nas exportações de dados através das APIs de Autoria LUIS (também conhecidas como Programáticas):

| | **Conta de Utilizador** | **Aplicação** | **Declarações** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Localização da aprendizagem ativa

Para permitir a [aprendizagem ativa,](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)as expressões registadas pelos utilizadores, recebidas nos pontos finais da LUIS publicadas, são armazenadas nas seguintes geografias Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [E.U.A.](#united-states)

Com exceção dos dados de aprendizagem ativa (detalhados abaixo), a LUIS segue as [práticas de armazenamento de dados para os serviços regionais.](https://azuredatacentermap.azurewebsites.net/)

### <a name="europe"></a>Europa

O [portal eu.luis.ai](https://eu.luis.ai) e a Autoria da Europa (também conhecidas como APIs programáticas) são hospedados na geografia europeia de Azure. O portal eu.luis.ai e a Europe Authoring (também conhecidas como APIs programáticas) apoiam a implantação de pontos finais para as seguintes geografias Azure:

* Europa
* França
* Reino Unido

Ao implementar estas geografias Azure, as declarações recebidas pelo ponto final dos utilizadores finais da sua app serão armazenadas na geografia europeia do Azure para uma aprendizagem ativa. Pode desativar a aprendizagem ativa, ver [Desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Para gerir as expressões armazenadas, consulte [Apagar as expressões](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Austrália

O [portal au.luis.ai](https://au.luis.ai) e a Australian Authoring (também conhecida como APIs Programáticas) são hospedados na geografia australiana de Azure. O portal au.luis.ai e a Australia Authoring (também conhecidas como APIs programáticas) suportam a implantação de pontos finais para as seguintes geografias Azure:

* Austrália

Ao implementar estas geografias Azure, as declarações recebidas pelo ponto final dos utilizadores finais da sua app serão armazenadas na geografia australiana do Azure para uma aprendizagem ativa. Pode desativar a aprendizagem ativa, ver [Desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Para gerir as expressões armazenadas, consulte [Apagar as expressões](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>Estados Unidos da América

O [portal luis.ai](https://www.luis.ai) e a Autoria dos Estados Unidos (também conhecidas como APIs programáticas) estão hospedados na geografia dos Estados Unidos da Azure. O portal luis.ai e a Autoria dos Estados Unidos (também conhecidas como APIs programáticas) apoiam a implantação de pontos finais para as seguintes geografias Azure:

* Geografias azuis não apoiadas pelas regiões autorais da Europa ou da Austrália

Ao implementar estas geografias Azure, as declarações recebidas pelo ponto final dos utilizadores finais da sua app serão armazenadas na geografia dos Estados Unidos da Azure para uma aprendizagem ativa. Pode desativar a aprendizagem ativa, ver [Desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Para gerir as expressões armazenadas, consulte [Apagar as expressões](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência das regiões LUIS](./luis-reference-regions.md)
