---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467307"
---
Vá ao portal Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" crie um novo recurso Desío reconhecimento de " target="_blank"> formulários crie um novo recurso de Reconhecimento de </a> Formulários . No painel **Criar,** forneça as seguintes informações:

| Detalhes do projeto   | Descrição   |
|--|--|
| **Subscrição** | Selecione a subscrição Azure a quem foi concedido acesso. |
| **Grupo de recursos** | O [grupo de recursos Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |
| **Região** | A localização do seu serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
| **Nome** | Um nome descritivo para o seu recurso. Recomendamos a utilização de um nome descritivo, por exemplo *MyNameFormRecognizer*. |
| **Escalão de preço** | O custo do seu recurso depende do nível de preços que escolher e da sua utilização. Para mais informações, consulte os [detalhes dos preços da](https://azure.microsoft.com/pricing/details/cognitive-services/)API.

> [!NOTE]
> Normalmente, quando cria um recurso de Serviço Cognitivo no portal Azure, tem a opção de criar uma chave de subscrição multi-serviço (usada em vários serviços cognitivos) ou uma chave de subscrição de um único serviço (usada apenas com um serviço cognitivo específico). No entanto, atualmente o Form Recogniser não está incluído na subscrição multi-serviço.

Quando o seu recurso 'Reconhecimento de Formulários' terminar a implantação, encontre-o e selecione-o a partir da lista **de todos os recursos** do portal. A sua chave e ponto final estarão localizados na página chave e ponto final do recurso, sob gestão de recursos. Guarde ambos para um local temporário antes de avançar.
