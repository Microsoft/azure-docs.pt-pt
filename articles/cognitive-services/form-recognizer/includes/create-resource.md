---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: d322aa27c7c9dcf4ad365ec22f8074aeacc85d3b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502834"
---
Quando são concedido acesso ao utilizar o reconhecedor de formulário, receberá um bem-vindo e-mail com várias ligações e os recursos. Utilize a ligação de "Portal do Azure" nessa mensagem para abrir o portal do Azure e criar um recurso do reconhecedor de formulário. Na **criar** painel, forneça as seguintes informações:

|    |    |
|--|--|
| **Name** | Um nome descritivo para o seu recurso. Recomendamos que utilize um nome descritivo, por exemplo *MyNameFormRecognizer*. |
| **Subscrição** | Selecione a subscrição do Azure que tenha sido concedida acesso. |
| **Location** | A localização da sua instância de serviço cognitivo. Localizações diferentes podem introduzir a latência, mas não tiver nenhum impacto sobre a disponibilidade de tempo de execução do seu recurso. |
| **Escalão de preço** | O custo do seu recurso depende do escalão de preço que escolher e a sua utilização. Para obter mais informações, consulte a API [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

> [!IMPORTANT]
> Normalmente quando criar um recurso de serviço cognitivo no portal do Azure, terá a opção para criar uma chave de assinatura de múltiplos serviços (que é utilizado em vários serviços cognitivos) ou uma chave de subscrição de serviço único (utilizado apenas com um serviço cognitivo específico). No entanto, como o reconhecedor de formulário é uma versão de pré-visualização, não está incluído na subscrição múltiplos serviço, e não é possível criar a subscrição de serviço único, a menos que utilize a ligação fornecida no e-mail de boas-vindos.

Quando o recurso do reconhecedor de forma a conclusão da implantação, localize e selecione-o a partir da **todos os recursos** lista no portal. Em seguida, selecione o **chaves** separador para ver as chaves de subscrição. Nenhuma das chaves fornecerá o acesso a aplicações para o recurso. Copie o valor da **chave 1**. Irá utilizá-lo na próxima seção.