---
title: Resolução de problemas - Personalizer
description: Este artigo contém respostas para perguntas frequentes sobre o Personalizer.
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 432b33243bdb38cf359d4fea1a336500eb244464
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650525"
---
# <a name="personalizer-troubleshooting"></a>Resolução de problemas personalizador

Este artigo contém respostas para perguntas frequentes sobre o Personalizer.

## <a name="transaction-errors"></a>Erros de transação

<details>
<summary><b>Recebo uma resposta HTTP 429 (muitos pedidos) do serviço. O que posso fazer?</b></summary>

**Resposta**: Se escolheu um nível de preço gratuito quando criou a instância Personalizer, existe um limite de quota no número de pedidos de Rank que são permitidos. Reveja a sua taxa de chamada API para o Rank API (no painel de métricas no portal Azure para o seu recurso Personalizer) e ajuste o nível de preços (no painel de nível de preços) se se esperar que o seu volume de chamadas aumente para além do limiar para o nível de preços escolhido.

</details>

<details>
<summary><b>Estou a ter um erro de 5xx no Rank ou na Reward APIs. O que devo fazer?</b></summary>

**Resposta**: Estas questões devem ser transparentes. Se continuarem, contacte o suporte selecionando **Novo pedido** de suporte na secção Suporte + resolução de **problemas,** no portal Azure para o seu recurso Personalizer.

</details>

## <a name="learning-loop"></a>Ciclo de aprendizagem

<details>
<summary>
<b>O ciclo de aprendizagem não corresponde a 100% do sistema sem o Personalizer. Como posso resolver isto?</b></summary>

**Resposta**: As razões pelas quais não alcança o seu objetivo com o ciclo de aprendizagem:
* Não há funcionalidades suficientes enviadas com chamada API rank
* Bugs nas funcionalidades enviadas - tais como o envio de dados de funcionalidades não agregados, tais como selos de tempo para rank API
* Bugs com processamento de loop - como não enviar dados de recompensa para Reward API para eventos

Para corrigir, é necessário alterar o processamento alterando as funcionalidades enviadas para o loop, ou certificar-se de que a recompensa é uma avaliação correta da qualidade da resposta do Rank.

</details>

<details>
<summary>
<b>O ciclo de aprendizagem não parece aprender. Como posso resolver isto?</b></summary>

**Resposta**: O ciclo de aprendizagem precisa de alguns milhares de chamadas de recompensa antes que as chamadas de Rank priorizem eficazmente.

Se não tem a certeza de como o seu ciclo de aprendizagem se está a comportar, faça uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizagem corrigida.

</details>

<details>
<summary><b>Continuo a obter resultados de classificação com todas as mesmas probabilidades para todos os itens. Como sei que o Personalizer está a aprender?</b></summary>

**Resposta**: O Personalizer devolve as mesmas probabilidades num resultado de API de Rank quando acaba de começar e tem um modelo _vazio,_ ou quando reinicia o Personalizer Loop, e o seu modelo ainda se encontra dentro do período de frequência de atualização do **Modelo.**

Quando o novo período de atualização começar, o modelo atualizado é utilizado e verá que as probabilidades mudam.

</details>

<details>
<summary><b>O ciclo de aprendizagem foi aprendendo, mas parece não aprender mais, e a qualidade dos resultados do Rank não é tão boa. O que devo fazer?</b></summary>

**Resposta:**
* Certifique-se de que concluiu e aplicou uma avaliação no portal Azure para esse recurso Personalizer (ciclo de aprendizagem).
* Certifique-se de que todas as recompensas são enviadas, através da API recompensa, e processadas.

</details>


<details>
<summary><b>Como sei que o ciclo de aprendizagem está a ser atualizado regularmente e é usado para marcar os meus dados?</b></summary>

**Resposta**: Pode encontrar o momento em que o modelo foi atualizado pela última vez na página de Definições de **Modelo e Aprendizagem** do portal Azure. Se vir um carimbo de tempo antigo, é provável que não esteja a enviar as chamadas de Rank and Reward. Se o serviço não tiver dados de entrada, não atualiza a aprendizagem. Se vir que o ciclo de aprendizagem não está a ser atualizado com frequência, pode editar a frequência de **Atualização**do Modelo do loop .

</details>

## <a name="offline-evaluations"></a>Avaliações offline

<details>
<summary><b>A importância da funcionalidade de uma avaliação offline devolve uma longa lista com centenas ou milhares de itens. O que aconteceu?</b></summary>

**Resposta**: Isto deve-se tipicamente a carimbos de tempo, iDs de utilizador ou algumas outras funcionalidades de grãos finos enviadas.

</details>

<details>
<summary><b>Criei uma avaliação offline e conseguiquase instantaneamente. E porquê? Não vejo resultados?</b></summary>

**Resposta**: A avaliação offline utiliza os dados do modelo treinados dos eventos nesse período de tempo. Se não tiver enviado quaisquer dados no período de tempo entre o início e o fim da avaliação, irá completar sem quaisquer resultados. Envie uma nova avaliação offline selecionando um intervalo de tempo com eventos que sabe terem sido enviados para personalizer.

</details>


## <a name="learning-policy"></a>Política de aprendizagem

<details>
<summary><b>Como posso importar uma política de aprendizagem?</b></summary>

**Resposta**: Saiba mais sobre [conceitos](concept-active-learning.md#understand-learning-policy-settings) de política de aprendizagem e [como aplicar](how-to-manage-model.md) uma nova política de aprendizagem. Se não quiser selecionar uma política de aprendizagem, pode usar a [avaliação offline](how-to-offline-evaluation.md) para sugerir uma política de aprendizagem, com base nos seus eventos atuais.

</details>

## <a name="security"></a>Segurança

<details>
<summary><b>A chave API para o meu loop foi comprometida. O que posso fazer?</b></summary>

**Resposta:** Pode regenerar uma tecla depois de trocar os seus clientes para utilizar a outra tecla. Ter duas chaves permite-lhe propagar a chave de forma preguiçosa sem ter de ter tempo de descanso. Recomendamos que o faça num ciclo regular como medida de segurança.

</details>

## <a name="next-steps"></a>Passos seguintes

[Configure a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)