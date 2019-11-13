---
title: Exportar & excluir dados-LUIS
titleSuffix: Azure Cognitive Services
description: Você tem controle total sobre a exibição, a exportação e a exclusão de seus dados. Exclua os dados do cliente para garantir a privacidade e a conformidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 061f019fe36e4d5495a41fc81e56d9673ad595fc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953465"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportar e eliminar os dados dos clientes na compreensão de idiomas (LUIS) nos serviços cognitivos

Exclua os dados do cliente para garantir a privacidade e a conformidade. 

## <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados do cliente
Language Understanding Intelligent Service (LUIS) preserva o conteúdo de cliente para operar o serviço, mas o utilizador de LUIS tem controlo total sobre a exibição, exportar e eliminar os seus dados. Isso pode ser feito por meio do [portal](luis-reference-regions.md) da Web do Luis ou das [APIs de criação do Luis (também conhecidas como programáticas)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Conteúdo de cliente é armazenado encriptada no armazenamento do Microsoft regional do Azure e inclui:

- Conteúdo de conta de utilizador recolhidas no registo
- Dados de treinamento necessários para criar os modelos
- Consultas de usuário registradas usadas pelo [aprendizado ativo](luis-concept-review-endpoint-utterances.md) para ajudar a melhorar o modelo
  - Os utilizadores podem desativar o registo de consultas, acrescentando `&log=false` para o pedido, detalhes [aqui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente
LUIS os usuários têm controle total para excluir qualquer conteúdo de usuário, seja por meio do portal da Web do LUIS ou das APIs de criação de LUIS (também conhecidas como programáticas). A tabela seguinte apresenta links ajudá-lo com ambos:

| | **Conta de utilizador** | **Aplicação** | **Expressão (s) de exemplo** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Ligação](luis-concept-data-storage.md#delete-an-account) | [Ligação](luis-how-to-start-new-app.md#delete-app) | [Ligação](luis-concept-data-storage.md#utterances-in-an-intent) | [Declarações de aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Declarações registrados](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportar dados do cliente
Os usuários do LUIS têm controle total para exibir os dados no portal, no entanto, eles devem ser exportados por meio das APIs de criação de LUIS (também conhecidas como programáticas). A tabela a seguir exibe links que auxiliam com exportações de dados por meio das APIs de criação LUIS (também conhecidas como programáticas):

| | **Conta de utilizador** | **Aplicação** | **Utterance(s)** | **Consultas de utilizador final** |
| --- | --- | --- | --- | --- |
| **APIs** | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ligação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Local do aprendizado ativo

Para habilitar o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md#enable-active-learning), os declarações registrados em log dos usuários, recebidos nos pontos de extremidade Luis publicados, são armazenados nas seguintes regiões geográficas do Azure:

* [Europa](#europe)
* [Austrália](#australia)
* [Estados Unidos](#united-states)

Com exceção dos dados de aprendizado ativos (detalhados abaixo), o LUIS segue as [práticas de armazenamento de dados para serviços regionais](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europa

O portal [eu.Luis.ai](https://eu.luis.ai) e a criação da Europa (também conhecidas como APIs programáticas) são hospedados na geografia da Europa do Azure. O portal do eu.luis.ai e a criação da Europa (também conhecida como APIs programáticas) dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Europa
* França
* Reino Unido

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia da Europa do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Austrália

O portal [au.Luis.ai](https://au.luis.ai) e a criação da Austrália (também conhecidas como APIs programáticas) são hospedados na geografia da Austrália do Azure. O portal do au.luis.ai e a criação da Austrália (também conhecida como APIs programáticas) dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Austrália

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia da Austrália do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Estados Unidos

O portal do [Luis.ai](https://www.luis.ai) e a criação de Estados Unidos (também conhecida como APIs programáticas) são hospedados na geografia de Estados Unidos do Azure. O portal do luis.ai e a criação de Estados Unidos (também conhecida como APIs programáticas) dão suporte à implantação de pontos de extremidade para as seguintes regiões geográficas do Azure:

* Geografias do Azure sem suporte nas regiões de criação da Europa ou da Austrália

Ao implantar nessas regiões do Azure, o declarações recebido pelo ponto de extremidade dos usuários finais do seu aplicativo será armazenado na geografia Estados Unidos do Azure para o aprendizado ativo. Você pode desabilitar o aprendizado ativo, consulte [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Para gerenciar o declarações armazenado, consulte [excluir expressão](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência de regiões de LUIS](./luis-reference-regions.md)
