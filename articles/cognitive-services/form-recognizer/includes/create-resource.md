---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 897f2b728dc068b09849d4f48f899b8630a87a51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004008"
---
Vá ao portal Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" crie um novo recurso Desío reconhecimento de " target="_blank"> formulários crie um novo recurso de Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários . No painel **Criar,** forneça as seguintes informações:

|    |    |
|--|--|
| **Name** | Um nome descritivo para o seu recurso. Recomendamos a utilização de um nome descritivo, por exemplo *MyNameFormRecognizer*. |
| **Subscrição** | Selecione a subscrição Azure a quem foi concedido acesso. |
| **Localização** | A localização do seu serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
| **Escalão de preço** | O custo do seu recurso depende do nível de preços que escolher e da sua utilização. Para mais informações, consulte os [detalhes dos preços da](https://azure.microsoft.com/pricing/details/cognitive-services/)API.
| **Grupo de recursos** | O [grupo de recursos Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

> [!NOTE]
> Normalmente, quando cria um recurso de Serviço Cognitivo no portal Azure, tem a opção de criar uma chave de subscrição multi-serviço (usada em vários serviços cognitivos) ou uma chave de subscrição de um único serviço (usada apenas com um serviço cognitivo específico). No entanto, atualmente o Form Recogniser não está incluído na subscrição multi-serviço.

Quando o seu recurso 'Reconhecimento de Formulários' terminar a implantação, encontre-o e selecione-o a partir da lista **de todos os recursos** do portal. A sua chave e ponto final estarão localizados na página chave e ponto final do recurso, sob gestão de recursos. Guarde ambos para um local temporário antes de avançar.