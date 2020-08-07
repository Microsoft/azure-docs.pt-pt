---
title: Configurar alertas
titleSuffix: Azure Digital Twins
description: Veja como ativar alertas nas métricas Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1b296cd942e36817da2832467ab603ebd833f825
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909866"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Resolução de problemas Azure Digital Twins: Alertas

A Azure Digital Twins recolhe [métricas](troubleshoot-metrics.md) para a sua instância de serviço que dão informações sobre o estado dos seus recursos. Você pode usar estas métricas para avaliar a saúde geral do serviço Azure Digital Twins e os recursos ligados a ele.

**Os alertas** notificam-no proativamente quando forem encontradas condições importantes nos dados das suas métricas. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Pode ler mais sobre alertas em [*Visão Geral dos alertas no Microsoft Azure.*](../azure-monitor/platform/alerts-overview.md)

## <a name="turn-on-alerts"></a>Ligue os alertas

Eis como ativar alertas para a sua instância Azure Digital Twins:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **Alertas** do menu e, em seguida, **+ Nova regra de alerta**.

    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Screenshot mostrando a página e o botão alertas para adicionar. Ainda não há alertas configurados." lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. Na página *de regras de alerta Create* que se segue, pode seguir as instruções para definir condições, ações a serem desencadeadas e alertar detalhes.     
    * **Os** detalhes do âmbito devem preencher automaticamente os detalhes da sua instância.
    * Irá definir detalhes do grupo **Condition** and **Action** para personalizar os gatilhos e respostas de alerta.

    :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Screenshot mostrando a página 'Criar Regra de Alerta' com secções para âmbito, condição e grupo de ação" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Para uma passagem guiada para preencher estes campos, consulte [*a visão geral dos alertas no Microsoft Azure*](../azure-monitor/platform/alerts-overview.md). Abaixo estão alguns exemplos de como serão os passos para a Azure Digital Twins.

Aqui está um excerto do processo *de condição Select* que ilustra quais os tipos de sinais de alerta disponíveis para as Gémeas Digitais Azure. Nesta página pode filtrar o tipo de sinal e selecionar o sinal que deseja de uma lista.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Screenshot mostrando a primeira página Configure Signal Logic. Há um destaque na caixa do tipo Signal para selecionar métricas ou registos de atividade, e uma lista de métricas por baixo que podem ser selecionadas":::

Depois de selecionar um sinal, ser-lhe-á pedido que configuure a lógica do alerta. Pode filtrar uma dimensão, definir um valor de limiar para o seu alerta e definir a frequência de verificações para a circunstância. Aqui está um exemplo de configuração de um alerta para quando a métrica média de taxa de falha de encaminhamento vai acima de 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Screenshot mostrando a segunda página Configure Signal Logic. A página mostra o histórico de métricas, tem uma área para filtrar sobre uma dimensão como operações de Grade de Eventos, e uma secção para definir lógica de alerta como "média é superior a 5"":::
 
Depois de configurar alertas, eles vão aparecer de volta na página *alertas,* para o seu exemplo.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Screenshot mostrando a página e o botão alertas para adicionar. Há um alerta configurado" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre alertas com o Azure Monitor, consulte [*a visão geral dos alertas no Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).
* Para obter informações sobre as métricas Azure Digital Twins, consulte [*resolução de problemas: Ver métricas com O Monitor Azure*](troubleshoot-metrics.md).
* Para ver como ativar o registo de diagnósticos para as suas métricas, consulte [*Resolução de Problemas: Configurar diagnósticos*](troubleshoot-diagnostics.md).
