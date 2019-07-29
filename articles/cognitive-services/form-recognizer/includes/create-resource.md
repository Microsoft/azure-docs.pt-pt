---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: dce9b4ffa05b48fc1789859328b221ac1a0f3a27
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594438"
---
Quando você tiver acesso para usar o reconhecedor de formulário, receberá um email de boas-vindas com vários links e recursos. Use o link "portal do Azure" nessa mensagem para abrir o portal do Azure e criar um recurso de reconhecimento de formulário. No painel **criar** , forneça as seguintes informações:

|    |    |
|--|--|
| **Name** | Um nome descritivo para seu recurso. É recomendável usar um nome descritivo, por exemplo, *MyNameFormRecognizer*. |
| **Subscrição** | Selecione a assinatura do Azure que recebeu acesso. |
| **Location** | O local de sua instância de serviço cognitiva. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do recurso. |
| **Escalão de preço** | O custo do recurso depende do tipo de preço que você escolher e do uso. Para obter mais informações, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API.
| **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) que conterá o recurso. Você pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

> [!IMPORTANT]
> Normalmente, quando você cria um recurso de serviço cognitiva no portal do Azure, você tem a opção de criar uma chave de assinatura de vários serviços (usada em vários serviços cognitivas) ou uma chave de assinatura de serviço único (usada somente com um serviço de cognitiva específico). No entanto, como o reconhecedor de formulário é uma versão de visualização, ele não está incluído na assinatura de vários serviços e você não pode criar a assinatura de serviço único, a menos que use o link fornecido no email de boas-vindas.

Quando o recurso do reconhecedor de formulário concluir a implantação, localize-o e selecione-o na lista **todos os recursos** no Portal. Em seguida, selecione a guia **chaves** para exibir suas chaves de assinatura. Qualquer chave dará ao aplicativo acesso ao recurso. Copie o valor da **chave 1**.