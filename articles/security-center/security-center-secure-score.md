---
title: Pontuação segura na central de segurança do Azure | Microsoft Docs
description: " Priorize suas recomendações de segurança usando a pontuação segura na central de segurança do Azure. "
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
ms.openlocfilehash: 0a39fd1870ae803f2cf694e01c67d125fc96cac9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200968"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhorar sua pontuação segura na central de segurança do Azure


Com tantos serviços que oferecem benefícios de segurança, muitas vezes é difícil saber quais etapas tomar primeiro para proteger e proteger sua carga de trabalho. A pontuação segura do Azure revisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações executar primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação. A classificação de segurança é uma ferramenta que o ajuda a avaliar a sua postura de segurança de carga de trabalho.

## <a name="secure-score-calculation"></a>Cálculo de Pontuação segura

A central de segurança imita o trabalho de um analista de segurança, examinando suas recomendações de segurança e aplicando algoritmos avançados para determinar a crucialidade de cada recomendação.
A central de segurança do Azure revisa constantemente as recomendações ativas e calcula sua pontuação segura com base nelas, a pontuação de uma recomendação é derivada de suas práticas recomendadas de gravidade e segurança que afetarão a segurança da sua carga de trabalho.

A central de segurança também fornece uma **Pontuação segura geral**. 

A **Pontuação de segurança geral** é um acúmulo de todas as suas pontuações de recomendação. Você pode exibir sua pontuação de segurança geral em suas assinaturas ou grupos de gerenciamento, dependendo do que você selecionar. A pontuação variará com base na assinatura selecionada e nas recomendações ativas nessas assinaturas.

 
Para verificar quais recomendações afetam a sua pontuação segura, você pode exibir as três principais recomendações mais impactantes no painel da central de segurança ou pode classificar as recomendações na folha da lista de recomendações usando o **impacto de Pontuação segura** pilha.


Para exibir sua pontuação de segurança geral:

1. No painel do Azure, clique em **central de segurança** e em **classificação segura**.
2. Na parte superior, você pode ver os destaques de Pontuação segura:
   - A **Classificação de segurança geral** representa a classificação por políticas, por subscrição selecionada
   - A **classificação segura por categoria** mostra quais recursos precisam mais de atenção
   - As **principais recomendações por impacto de Pontuação segura** fornecem uma lista das recomendações que melhorarão sua pontuação segura mais se você implementá-las.
 
   ![Pontuação segura](./media/security-center-secure-score/secure-score-dashboard.png)

3. Na tabela abaixo, você pode ver cada uma de suas assinaturas e a pontuação de segurança geral de cada uma.

   > [!NOTE]
   > A soma da classificação de segurança de cada subscrição não é igual à classificação de segurança geral. A classificação de segurança é um cálculo baseado na relação entre os seus recursos em bom estado de funcionamento e o total de recursos por recomendação, não uma soma das classificações de segurança nas suas subscrições. 
   >
4. Clique em **Exibir recomendações** para ver as recomendações para a assinatura que você pode corrigir para melhorar sua pontuação segura.
4. Na lista de recomendações, você pode ver que, para cada recomendação, há uma coluna que representa o **impacto de Pontuação segura**. Esse número representa o quanto sua pontuação de segurança geral será aprimorada se você seguir as recomendações. Por exemplo, na tela abaixo, se você **corrigir vulnerabilidades em configurações de segurança de contêiner**, sua pontuação segura aumentará em 35 pontos.

   ![Pontuação segura](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Pontuação segura individual

Além disso, para exibir pontuações seguras individuais, você pode encontrá-las na folha de recomendação individual.  

A **Classificação de segurança da recomendação** é um cálculo baseado na relação entre os recursos em bom estado de funcionamento e o total de recursos. Se o número de recursos em bom estado de funcionamento for igual ao número total de recursos, receberá a classificação de segurança máxima da recomendação de 50. Para tentar colocar a classificação de segurança mais próxima da classificação máxima, corrija os recursos em mau estado de funcionamento seguindo as recomendações.

O **Impacto das recomendações** permite-lhe saber quanto é que a sua classificação de segurança melhorará se aplicar os passos de recomendação. Por exemplo, se classificação de segurança for de 42 e o **Impacto das recomendações** for +3, ao realizar os passos descritos na recomendação, melhora a classificação para 45.

A recomendação mostra a quais ameaças sua carga de trabalho será exposta se as etapas de correção não forem executadas.

![Pontuação segura de recomendação individual](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Passos seguintes
Este artigo mostrou como melhorar sua postura de segurança usando a **Pontuação segura** na central de segurança do Azure. Para saber mais sobre a central de segurança, consulte:

* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.


