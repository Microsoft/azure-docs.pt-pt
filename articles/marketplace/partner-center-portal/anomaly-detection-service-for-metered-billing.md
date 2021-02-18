---
title: Serviço de deteção de anomalias para faturação medido - Microsoft Azure Marketplace
description: Descreve como funciona a deteção de anomalias, quando as notificações são enviadas e o que fazer com elas, e opções de suporte.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: ac3e07c67ca82c2960de1c4341a714b33751bfc1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092405"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Serviço de deteção de anomalias da faturação limitada

O [serviço de medição marketplace](marketplace-metering-service-apis-faq.md) permite criar ofertas no programa de marketplace comercial que são cobradas de acordo com unidades não padrão. Com a faturação medido, envia eventos de utilização para o uso do seu cliente para a Microsoft e preparamos a faturação com base nessa utilização.

Dados de utilização incorretos podem vir de uma variedade de causas, tais como bugs, configurações erradas no seu rastreio de consumo, ou fraude. Dados de utilização incorretos resultariam em encargos incorretos do cliente e em litígios de faturação.

Para mitigar o risco, o nosso serviço de deteção de anomalias aplica algoritmos de aprendizagem automática para determinar o comportamento normal de faturação medido, analisar o uso de faturação medido e descobrir anomalias com a mínima intervenção do utilizador.

É notificado se houver anomalia detetada no seu uso de faturação medido. Isto dá-lhe a oportunidade de investigar e notificar-nos se uma anomalia for confirmada como um problema real, altura em que podem ser tomadas medidas para abordar proativamente a questão da faturação do cliente.

Além de picos repentinos, mergulhos e mudanças de tendência do uso da faturação medido, o nosso modelo também explica os efeitos sazonais. Como a faturação medido é comunicada através de dados de excesso de idade, o nosso modelo também é capaz de lidar graciosamente com longos períodos de dados em falta.

Seguem-se exemplos de resultados de deteção de anomalias. O alcance esperado mostra-se como uma banda amarela. O uso aceitável de faturação medido mostra como estrelas verdes na banda. O uso de faturação fora da banda apresenta-se como um ponto vermelho.  

Anomalias detetadas fora de uma tendência previsível:

![Ilustra anomalias detetadas fora de uma tendência previsível.](media/anomaly-1.png)

Anomalias detetadas fora de uma tendência cíclica recorrente:

![Ilustra anomalias detetadas fora de uma tendência cíclica recorrente.](media/anomaly-2.png)

Anomalias detetadas numa tendência ascendente:

![Ilustra anomalias detetadas numa tendência ascendente.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Como funciona o serviço de deteção de anomalias

A deteção de anomalias é ativada automaticamente para todo o uso da faturação medido. Quando envia os eventos de utilização para a Microsoft, o serviço de deteção de anomalias cria um modelo de valores esperados com base em dados de utilização anteriores. Este modelo funciona semanalmente.

Funções de deteção de anomalias a nível por metro e por cliente. Isto significa que cada medidor com cada cliente terá um modelo treinado com base no padrão de utilização passado deste cliente deste contador.

O modelo funciona gerando intervalos de confiança retrospetiva. A previsão da série de tempo é um modelo aditivo generalizado que consiste numa parte de previsão de tendência e numa parte da sazonalidade. Como o modelo é formulado como uma tarefa de regressão, pode lidar graciosamente com longos períodos de falta de dados. Se uma observação cair fora dos intervalos de confiança previstos, significa que a observação não pode ser explicada com base em padrões históricos da faturação medida e, portanto, pode ser uma anomalia.

## <a name="anomaly-detection-notification"></a>Notificação de deteção de anomalias

Pode avaliar, gerir e reconhecer anomalias no Partner Center. Para saber como, consulte [Gerir anomalias de faturação medidos no Partner Center](../anomaly-detection.md).

Para garantir que os seus clientes não estão sobrecarregados com o uso medido, deve investigar se anomalias detetadas são problemas reais. Em caso afirmativo, pode reconhecer o uso incorreto no Centro de Parceiros.

Recomendamos que confirme se anomalias detetadas são de uso normal. Ao fazê-lo, melhorará os dados de anomalia que lhe fornecemos. Se uma anomalia representar um risco financeiro potencialmente elevado, podemos contactá-lo para confirmar o uso.

## <a name="when-and-how-to-get-support"></a>Quando e como obter apoio

Se nos enviou uma utilização com contadores incorretos que o fez ou resultará numa sobretaxa para o cliente, não iniciaremos uma fatura ao cliente para utilização sub-reportada ou pagar-lhe-emos por esse uso. Terá de suportar a perda de receitas devido à comunicação de valor inferior.

Se um dos seguintes casos se aplicar, pode ajustar o valor de utilização no Partner Center, o que resultará num reembolso ou ajuste de faturação para os seus clientes:

- Confirmou que uma das anomalias que encontrámos é um problema real e que o uso incorreto resultaria na sobrecarga do cliente.
- Descobre que nos enviou uma utilização incorreta e que o uso incorreto resultaria na sobrecarga do cliente.

Para submeter um bilhete de apoio relacionado com anomalias de faturação medido:

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) com a sua conta de trabalho.
1. No menu no canto superior direito da página, selecione o ícone **De suporte.** O **painel de ajuda e apoio** aparece no lado direito da página.
1. Para obter ajuda no mercado comercial, **selecione Commercial Marketplace**.
   ![Ilustra o painel de apoio.](../media/support/commercial-marketplace-support-pane.png)
1. Na caixa **de resumo de problemas,** entre no mercado comercial **> faturação medido.**
1. Na caixa **de tipo Problema,** selecione uma das seguintes:
    - **Marketplace Comercial > Faturação Medido > Uso errado enviado para oferta de aplicações Azure**
    - **Marketplace Comercial > Faturação Medido > Uso errado enviado para oferta saas**
1. No **passo seguinte**, selecione **Soluções de Revisão**.
1. Reveja os documentos recomendados, se algum ou selecionar **Forneça detalhes de emissão** para submeter um bilhete de apoio.

Para obter mais opções de suporte ao editor, consulte [Suporte para o programa de marketplace comercial no Partner Center.](../support.md)

## <a name="next-steps"></a>Passos seguintes

- Conheça o [serviço de medição marketplace API.](marketplace-metering-service-apis.md)
- [Gerir anomalias de faturação medidos no Partner Center](../anomaly-detection.md)
