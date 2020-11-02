---
title: Métricas Consultor bibliotecas cliente REST API
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para ligar as suas aplicações à API do Advisor Métricas da Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186857"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Quickstart: Utilize as bibliotecas do cliente ou ASPis REST para personalizar a sua solução

Inicie com as bibliotecas do Advisor de Métricas REST API ou do cliente. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize o Advisor métrica para executar:

* Adicionar um feed de dados de uma fonte de dados
* Verifique o estado da ingestão
* Deteção e alertas de configuração 
* Consultar os resultados da deteção de anomalias
* Diagnosticar anomalias


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o portal Web](web-portal.md)
- [A bordo dos feeds dos seus dados](../how-tos/onboard-your-data.md)
    - [Gerir feeds de dados](../how-tos/manage-data-feeds.md)
    - [Configurações para diferentes fontes de dados](../data-feeds-from-different-sources.md)
- [Configurar métricas e otimizar a configuração da deteção](../how-tos/configure-metrics.md)
- [Ajustar a deteção de anomalias através de comentários](../how-tos/anomaly-feedback.md)