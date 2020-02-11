---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 0a4faad8bfe92a5389e0ee440aa3ccc404535955
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118087"
---
Vá ao Portal Azure e [crie um novo recurso de Reconhecimento](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)de Formulários. No painel **Criar,** forneça as seguintes informações:

|    |    |
|--|--|
| **Nome** | Um nome descritivo para o seu recurso. Recomendamos a utilização de um nome descritivo, por exemplo *MyNameFormRecogniser*. |
| **Subscrição** | Selecione a subscrição Azure que foi concedida acesso. |
| **Localização** | A localização da sua instância de serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
| **Escalão de preço** | O custo do seu recurso depende do nível de preços que escolher e da sua utilização. Para mais informações, consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API .
| **Grupo de recursos** | O [grupo de recursos Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

> [!IMPORTANT]
> Normalmente, quando se cria um recurso do Serviço Cognitivo no portal Azure, tem a opção de criar uma chave de subscrição multi-serviço (usada em vários serviços cognitivos) ou uma chave de subscrição de um serviço único (usada apenas com um serviço cognitivo específico). No entanto, como o Form Recogniser é um lançamento de pré-visualização, não está incluído na subscrição de vários serviços, e não pode criar a subscrição de serviço único a menos que utilize o link fornecido no e-mail Welcome.

Quando o seu recurso 'Reconhecimento de Formulários' terminar a implantação, encontre-o e selecione-o a partir da lista **de todos os recursos** no portal. Em seguida, selecione o separador **de arranque Rápido** para visualizar os seus dados de subscrição. Guarde os valores de **Key1** e **Endpoint** para uma localização temporária. Vaiusá-los nos seguintes passos.