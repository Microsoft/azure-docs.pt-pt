---
title: Serviço de deteção de anomalias para faturação medido - Microsoft Azure Marketplace
description: Descreve como funciona a deteção de anomalias, quando as notificações são enviadas e o que fazer com elas, e opções de suporte.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: cb134c82f231eb8b6329b1acafb181032edd6936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320090"
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

Enviamos avisos de deteção de anomalias por e-mail semanalmente. Inclui todas as anomalias detetadas naquela semana para todos os contadores e clientes. Este e-mail é enviado para os contactos **de Engenharia** e **Apoio** fornecidos quando criou a oferta.

Espera-se que investigue se as anomalias detetadas são problemas reais e, em caso afirmativo, contacte a Microsoft para reportar a utilização incorreta (consulte a secção de suporte abaixo).

Se confirmar que anomalias detetadas são uma utilização normal, não é necessária mais nenhuma ação por si. No entanto, se uma anomalia representar um risco financeiro potencialmente elevado, poderemos contactá-lo para confirmar o uso.  

## <a name="when-and-how-to-get-support"></a>Quando e como obter apoio

Se enviou a utilização incorreta para a Microsoft e isso resultou ou resultará num subcomisse do cliente, a Microsoft não iniciará uma fatura ao cliente para utilização sub-reportada ou pagar-lhe-á por essa utilização. Terá de suportar a perda de receitas devido à comunicação de valor inferior.

Se um dos seguintes casos se aplicar, poderá abrir um pedido de suporte para pedir um reembolso ou um ajuste da faturação para os clientes:

- Confirmou que uma das anomalias que encontrámos é um problema real e que o uso incorreto resultaria numa **sobrecarga** do cliente.
- Descobre que nos enviou uma utilização incorreta e que o uso incorreto resultaria numa **sobrecarga** do cliente.
- Você gostaria de solicitar um reembolso para o custo do uso de faturação medido do seu cliente.

Para submeter um bilhete:

1. Vá à página de apoio. No Tell us sobre a **sua caixa de emissão,** insira "uso errado".
2. Em tópicos de apoio, no drop-down dos resultados da pesquisa, selecione um dos seguintes:
    - **Mercado**  >  Comercial **Faturação medido**  >  **Uso errado enviado para oferta de aplicações Azure,** ou
    - **Mercado**  >  Comercial **Faturação medido**  >  **Uso errado enviado para oferta SaaS**
3. No **passo seguinte**, selecione o botão de **soluções de Avaliação** a direcionar para iniciar sação no Partner Center para submeter um bilhete de apoio.

Para obter mais opções de suporte ao editor, consulte [Suporte para o programa de marketplace comercial no Partner Center.](support.md)

## <a name="next-step"></a>Passo seguinte

- Conheça o [serviço de medição marketplace API.](marketplace-metering-service-apis.md)
