---
title: Inscreva-se a API de análise de texto
titleSuffix: Azure Cognitive Services
description: Instruções para inscrever-se e utilizar o serviço de análise de texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449968"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Como inscrever-se para a API de análise de texto

Os recursos de análise de texto estão disponíveis permanentemente na cloud. Antes de poder carregar os seus conteúdos para análise, tem de se inscrever para obter uma chave de acesso. Cada chamada à API requer uma chave de acesso no pedido.

+ Comece com uma subscrição do Azure. Pode criar uma [conta gratuita](https://azure.microsoft.com/free/) para experimentar sem custos.

+ Escolha a **API de Análise de Texto** para criar uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). A chave é gerada quando se inscreve.

A Análise de Texto tem disponível um escalão Gratuito para exploração e avaliação, bem como escalões faturáveis para cargas de trabalho de produção. Pode ter várias inscrições em cada assinatura: um gratuito, um paga e assim por diante. Se o seu volume de pedidos aumentar, pode mudar para um escalão que ofereça mais transações.

Os serviços em pré-visualização ou no escalão Gratuito não têm contrato de nível de serviço. Para obter mais informações, veja [SLA para Serviços Cognitivos](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>Como alterar o escalão

Comece o escalão Gratuito e mude para um faturável para cargas de trabalho de produção. A faturação standard é oferecida em níveis proporcionais. Pode mudar de escalão e manter o mesmo ponto final e as mesmas chaves de acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e [localize o seu serviço](text-analytics-how-to-access-key.md).

2. Clique em **Escalão de preço**.

   ![Comando do escalão de preço no menu de navegação do lado esquerdo](../media/portal-pricing-tier.png)

3. Escolha um escalão e clique em **Selecionar**.  Os limites novos entram em vigor assim que a seleção for processada. 

   ![Mosaicos e botão Selecionar na página de seleção do escalão](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Como funciona a faturação

A faturação é calculada com base no número de transações. Pode comprar um bloco de transações num determinado escalão num ciclo de faturação mensal e, se o exceder, é aplicada uma pequena taxa de utilização excedida por transação. Se exceder o limite máximo regularmente, considere mudar para um escalão mais alto.

Veja a [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) para obter mais informações.

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>O que constitui uma transação na API de Análise de Texto?
Qualquer anotação num documento conta como uma transação. As chamadas de classificação de batch também considerarão o número de documentos que devem ser classificados nessa transação. Por exemplo, se forem enviados 1000 documentos para análise de sentimentos numa única chamada à API, irão contar como 1000 transações.

## <a name="see-also"></a>Consulte também 

 [Text Analytics Overview](../overview.md) (Descrição Geral da Análise de Texto)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Get an access key](text-analytics-how-to-access-key.md) (Obter uma chave de acesso)
