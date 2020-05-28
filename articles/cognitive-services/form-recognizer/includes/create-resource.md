---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996805"
---
Vá ao portal Azure e crie um novo recurso do Reconhecimento de Formulários criar um novo recurso de Reconhecimento de <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" " target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários. No painel **Criar,** forneça as seguintes informações:

|    |    |
|--|--|
| **Nome** | Um nome descritivo para o seu recurso. Recomendamos a utilização de um nome descritivo, por exemplo *MyNameFormRecogniser*. |
| **Subscrição** | Selecione a subscrição Azure que foi concedida acesso. |
| **Localização** | A localização da sua instância de serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
| **Nível de preços** | O custo do seu recurso depende do nível de preços que escolher e da sua utilização. Para mais informações, consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API .
| **Grupo de recursos** | O [grupo de recursos Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

> [!NOTE]
> Normalmente, quando se cria um recurso do Serviço Cognitivo no portal Azure, tem a opção de criar uma chave de subscrição multi-serviço (usada em vários serviços cognitivos) ou uma chave de subscrição de um serviço único (usada apenas com um serviço cognitivo específico). No entanto, como o Form Recogniser é um lançamento de pré-visualização, não está incluído na subscrição de vários serviços, e não pode criar a subscrição de serviço único a menos que utilize o link fornecido no e-mail Welcome.

Quando o seu recurso 'Reconhecimento de Formulários' terminar a implantação, encontre-o e selecione-o a partir da lista **de todos os recursos** no portal. A sua chave e ponto final estarão localizados na página chave e ponto final do recurso, sob gestão de recursos. Guarde ambos para um local temporário antes de avançar.
