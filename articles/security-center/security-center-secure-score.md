---
title: Pontuação segura no Centro de Segurança Azure [ Microsoft Docs
description: " Priorize as suas recomendações de segurança utilizando a Pontuação Segura no Centro de Segurança Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415776"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhore a sua pontuação segura no Centro de Segurança Azure

> [!NOTE]
> Este artigo é sobre a versão anterior da pontuação segura. Esta experiência de pontuação segura ainda está disponível a partir da UI, mas será eliminada ao longo do tempo. As duas experiências de pontuação seguras estão a correr lado a lado para permitir uma transição mais suave.
>
> Para mais detalhes sobre a pontuação mais recente, consulte [aqui](secure-score-security-controls.md).
>

Com tantos serviços que oferecem benefícios de segurança, muitas vezes é difícil saber que passos tomar primeiro para garantir e endurecer a sua carga de trabalho. O Secure Score analisa as suas recomendações de segurança e prioriza-as para si, para que saiba quais as recomendações a executar primeiro. Isto ajuda-o a encontrar as vulnerabilidades de segurança mais graves para que possa priorizar a investigação. Secure Score é uma ferramenta que o ajuda a avaliar a sua postura de segurança da carga de trabalho.

## <a name="secure-score-calculation"></a>Cálculo de Pontuação Segura

O Security Center imita o trabalho de um analista de segurança, analisando as suas recomendações de segurança e aplicando algoritmos avançados para determinar o quão crucial cada recomendação é.
O Azure Security Center revê constantemente as suas recomendações ativas e calcula a sua Pontuação Segura com base nelas, a pontuação de uma recomendação deriva da sua gravidade e das melhores práticas de segurança que mais afetarão a sua segurança na carga de trabalho.

O Security Center também lhe fornece uma **pontuação geral segura**. 

**Overall Secure Score** é uma acumulação de todas as suas pontuações de recomendação. Pode ver o seu Secure Score geral através das suas subscrições ou grupos de gestão, dependendo do que selecionar. A pontuação variará em função da subscrição selecionada e das recomendações ativas sobre estas subscrições.

Para verificar quais as recomendações que mais impactam a sua Pontuação Segura, pode ver as três principais recomendações mais impactantes no painel do Centro de Segurança ou pode ordenar as recomendações na lâmina da lista de recomendações utilizando a coluna de **impacto Secure Score.**

Para ver o seu Resultado Seguro geral:

1. No painel azure, clique em **Security Center** e, em seguida, clique em **Secure Score**.

2. No topo pode ver os destaques do Secure Score:
   - O **Overall Secure Score** representa a pontuação por políticas, por subscrição selecionada
   - **Secure Score por categoria** mostra quais os recursos que mais precisam de atenção
   - **As principais recomendações do impacto Secure Score** fornecem-lhe uma lista das recomendações que melhorarão mais a sua Pontuação Segura se as implementar.
 
   ![Pontuação Segura](./media/security-center-secure-score/secure-score-dashboard.png)

3. Na tabela abaixo pode ver cada uma das suas subscrições e a pontuação segura geral para cada um.

   > [!NOTE]
   > A soma da Pontuação Segura de cada subscrição não é igual à pontuação segura global. O Secure Score é um cálculo baseado na relação entre os seus recursos saudáveis e os seus recursos totais por recomendação, e não uma soma de Pontuações Seguras em todas as suas subscrições. 
   >
4. Clique em **ver recomendações** para ver as recomendações para essa subscrição que pode remediar para melhorar a sua Pontuação Segura.
4. Na lista de recomendações, pode ver que para cada recomendação existe uma coluna que representa o **impacto secure score**. Este número representa o quanto o seu Secure Score irá melhorar se seguir as recomendações. Por exemplo, no ecrã abaixo, se **remediar vulnerabilidades em configurações**de segurança de contentores, a sua Pontuação Segura aumentará em 35 pontos.

   ![Pontuação Segura](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Pontuação individual segura

Além disso, para visualizar pontuações seguras individuais, pode encontrá-las dentro da lâmina de recomendação individual.  

A **Pontuação Segura** de Recomendação é um cálculo baseado na relação entre os seus recursos saudáveis e os seus recursos totais. Se o número de recursos saudáveis for igual ao número total de recursos, obtém-se a pontuação máxima segura da recomendação de 50. Para tentar aproximar o seu Secure Score da pontuação máxima, corrija os recursos pouco saudáveis seguindo as recomendações.

O **impacto da Recomendação** permite-lhe saber quanto melhora a sua Pontuação Segura se aplicar os passos de recomendação. Por exemplo, se a sua Pontuação Segura for de 42 e o **impacto da Recomendação** for +3, executar os passos descritos na recomendação melhore a sua pontuação para se tornar 45.

A recomendação mostra quais as ameaças a que a sua carga de trabalho está exposta se as medidas de reparação não forem tomadas.

![pontuação segura recomendação individual](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Passos seguintes
Este artigo mostrou-lhe como melhorar a sua postura de segurança usando **secure score** no Azure Security Center. Para saber mais sobre o Centro de Segurança, consulte:

* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
