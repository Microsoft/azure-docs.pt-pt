---
title: Configurar alertas
titleSuffix: Azure Digital Twins
description: Veja como ativar alertas nas métricas Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 461d7a82854ce62ee99eef1227c13c7a8f0371e2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594873"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Resolução de problemas Azure Digital Twins: Alertas

A Azure Digital Twins recolhe [métricas](troubleshoot-metrics.md) para a sua instância de serviço que dão informações sobre o estado dos seus recursos. Você pode usar estas métricas para avaliar a saúde geral do serviço Azure Digital Twins e os recursos ligados a ele.

**Os alertas** notificam-no proativamente quando forem encontradas condições importantes nos dados das suas métricas. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Pode ler mais sobre alertas em [*Visão Geral dos alertas no Microsoft Azure.*](../azure-monitor/alerts/alerts-overview.md)

## <a name="turn-on-alerts"></a>Ligue os alertas

Eis como ativar alertas para a sua instância Azure Digital Twins:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **Alertas** do menu e, em seguida, **+ Nova regra de alerta**.

3. Na página *de regras de alerta Create* que se segue, pode seguir as instruções para definir condições, ações a serem desencadeadas e alertar detalhes.     
    * **Os** detalhes do âmbito devem preencher automaticamente com os detalhes para o seu exemplo
    * Você definirá detalhes do grupo **de Condições** e **Ação** para personalizar gatilhos e respostas de alerta
    * Na secção de detalhes da **regra alerta,** insira um nome e descrição opcional para a sua regra. Pode selecionar a _regra de alerta Desativar a_ caixa de verificação de criação se quiser que o alerta fique ativo assim que for criado.
        - É também aqui que seleciona um _grupo de recursos_ e um nível de _Severidade._

4. Selecione o botão _de regra de alerta Criar_ a sua regra de alerta.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Screenshot mostrando a página 'Criar Regra de Alerta' com secções para âmbito, condição, grupo de ação e detalhes da regra de alerta" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Para uma passagem guiada para preencher estes campos, consulte [*a visão geral dos alertas no Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md). Abaixo estão alguns exemplos de como serão os passos para a Azure Digital Twins.

### <a name="select-conditions"></a>Selecione condições

Aqui está um excerto do processo *de condição Select* que ilustra quais os tipos de sinais de alerta disponíveis para as Gémeas Digitais Azure. Nesta página pode filtrar o tipo de sinal e selecionar o sinal que deseja de uma lista.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Screenshot mostrando a primeira página Configure Signal Logic. Há um destaque na caixa do tipo Signal para selecionar métricas ou registos de atividade, e uma lista de métricas por baixo que podem ser selecionadas":::

Depois de selecionar um sinal, ser-lhe-á pedido que configuure a lógica do alerta. Pode filtrar uma dimensão, definir um valor de limiar para o seu alerta e definir a frequência de verificações para a circunstância. Aqui está um exemplo de configuração de um alerta para quando a métrica média de taxa de falha de encaminhamento vai acima de 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Screenshot mostrando a segunda página Configure Signal Logic. A página mostra o histórico de métricas, tem uma área para filtrar sobre uma dimensão como operações de Grade de Eventos, e uma secção para definir lógica de alerta como &quot;média é superior a 5&quot;":::

### <a name="verify-success"></a>Verificar o sucesso

Depois de configurar alertas, eles vão aparecer de volta na página *alertas,* para o seu caso.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Screenshot mostrando a página e o botão alertas para adicionar. Há um alerta configurado" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre alertas com o Azure Monitor, consulte [*a visão geral dos alertas no Microsoft Azure*](../azure-monitor/alerts/alerts-overview.md).
* Para obter informações sobre as métricas Azure Digital Twins, consulte [*resolução de problemas: Ver métricas com O Monitor Azure*](troubleshoot-metrics.md).
* Para ver como ativar o registo de diagnósticos para as suas métricas, consulte [*Resolução de Problemas: Configurar diagnósticos*](troubleshoot-diagnostics.md).
