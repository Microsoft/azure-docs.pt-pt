---
title: Exportar e eliminar dados
titleSuffix: Azure Cognitive Services
description: Elimine dados de cliente para garantir a privacidade e conformidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895161"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e eliminar os dados dos clientes na compreensão de idiomas (LUIS) nos serviços cognitivos

Elimine dados de cliente para garantir a privacidade e conformidade. 

## <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente
Language Understanding Intelligent Service (LUIS) preserva o conteúdo de cliente para operar o serviço, mas o utilizador de LUIS tem controlo total sobre a exibição, exportar e eliminar os seus dados. Isso pode ser feito através da web de LUIS [portal](luis-reference-regions.md) ou o [LUIS criação APIs (também conhecido como programática)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Conteúdo de cliente é armazenado encriptada no armazenamento do Microsoft regional do Azure e inclui:

- Conteúdo de conta de utilizador recolhidas no registo
- Necessários para criar os modelos de dados de treinamento
- Com sessão iniciada consultas de utilizador utilizadas pelo [aprendizagem ativa](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os utilizadores podem desativar o registo de consultas, acrescentando `&log=false` para o pedido, detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente
Os utilizadores de LUIS tem controlo total para eliminar qualquer conteúdo, através do portal web do LUIS ou as APIs do LUIS (também conhecido como Programmatic) de criação de utilizador. A tabela seguinte apresenta links ajudá-lo com ambos:

| | **Conta de Utilizador** | **Aplicação** | **Utterance(s) de exemplo** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Ligação](luis-concept-data-storage.md#delete-an-account) | [Ligação](luis-how-to-start-new-app.md#delete-app) | [Ligação](luis-concept-data-storage.md#utterances-in-an-intent) | [Expressões de aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Expressões com sessão iniciada com](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportar dados do cliente
Os utilizadores de LUIS tem controlo total para ver os dados no portal, no entanto, tem de ser exportada através das APIs (também conhecido como programática) de criação do LUIS. A tabela seguinte apresenta links ajudá-lo com exportações de dados por meio das APIs (também conhecido como programática) LUIS criação:

| | **Conta de Utilizador** | **Aplicação** | **Utterance(s)** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Localização de aprendizagem ativa

Para habilitar [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#enable-active-learning), expressões com sessão iniciada dos utilizadores, recebidos em pontos de extremidade publicados do LUIS, são armazenados nas geografias do Azure seguintes:

* [Europa](#europe)
* [Austrália](#australia)
* [Estados Unidos](#united-states)

Com exceção dos dados de aprendizagem ativa (detalhados abaixo), LUIS segue a [práticas de armazenamento de dados para serviços regionais](http://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

O [eu.luis.ai](https://eu.luis.ai) portal e a Europa criação (também conhecido como APIs programáticas) estão alojados de localização geográfica de Europa do Azure. O portal de eu.luis.ai e, em seguida, na Europa criação (também conhecido como APIs programáticas) suportam a implementação de pontos de extremidade para as geografias do Azure seguintes:

* Europa
* França
* Reino Unido

Ao implementarem estas geografias do Azure, as expressões recebidas pelo ponto final dos utilizadores finais da sua aplicação serão armazenadas na área geográfica da Europa do Azure para aprendizagem ativa. Pode desativar a aprendizagem ativa, consulte [desativar a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerir expressões com armazenados, consulte [eliminar expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Austrália

O [au.luis.ai](https://au.luis.ai) portal e a Austrália criação (também conhecido como APIs programáticas) estão alojados na geografia do Azure da Austrália. O portal de au.luis.ai e, em seguida, Austrália criação (também conhecido como APIs programáticas) suportam a implementação de pontos de extremidade para as geografias do Azure seguintes:

* Austrália

Ao implementarem estas geografias do Azure, as expressões recebidas pelo ponto final dos utilizadores finais da sua aplicação serão armazenadas na área geográfica da Austrália do Azure para aprendizagem ativa. Pode desativar a aprendizagem ativa, consulte [desativar a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerir expressões com armazenados, consulte [eliminar expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Estados Unidos

O [luis.ai](https://www.luis.ai) portal e dos Estados Unidos (também conhecido como APIs programáticas) de criação estão alojados de localização geográfica de Estados Unidos do Azure. O portal de luis.ai e, em seguida, Estados Unidos (também conhecido como APIs programáticas) de criação suportam a implementação de pontos de extremidade para as geografias do Azure seguintes:

* Geografias do Azure não suportadas a Europa ou Austrália regiões de criação

Ao implementarem estas geografias do Azure, as expressões recebidas pelo ponto final dos utilizadores finais da sua aplicação serão armazenadas de localização geográfica de Estados Unidos do Azure para a aprendizagem ativa. Pode desativar a aprendizagem ativa, consulte [desativar a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerir expressões com armazenados, consulte [eliminar expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência de regiões de LUIS](./luis-reference-regions.md)
