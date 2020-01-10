---
title: Coletar dados de uma máquina virtual do Azure com Azure Monitor | Microsoft Docs
description: Saiba como ativar a Extensão de VM do agente do Log Analytics e a recolha de dados das VMs do Azure com o Log Analytics.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 85c953c4acdc31cc6d79600951ba745346771b0c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533973"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Início rápido: monitorar um recurso do Azure com o Azure Monitor
[Azure monitor](../overview.md) inicia a coleta de dados dos recursos do Azure no momento em que eles são criados. Este guia de início rápido fornece uma breve explicação dos dados coletados automaticamente para um recurso e como exibi-los no portal do Azure para um recurso específico. Posteriormente, você pode adicionar a configuração para coletar dados adicionais e acessar o menu de Azure Monitor para usar as mesmas ferramentas para acessar os dados coletados para todos os recursos em sua assinatura.

Para obter descrições mais detalhadas dos dados de monitoramento coletados dos recursos do Azure, consulte [monitorando recursos do Azure com o Azure monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Página de visão geral
Muitos serviços incluirão dados de monitoramento em sua página de **visão geral** como uma visão rápida de sua operação. Normalmente, isso será baseado em um subconjunto de métricas de plataforma armazenadas em métricas de Azure Monitor.

1. Localize um recurso do Azure em sua assinatura.
2. Vá para a página **visão geral** e observe se há dados de desempenho exibidos. Esses dados serão fornecidos por Azure Monitor. O exemplo a seguir é a página de **visão geral** de uma conta de armazenamento do Azure e você pode ver que há várias métricas exibidas.

    ![Página de visão geral](media/quick-monitor-azure-resource/overview.png)

3. Você pode clicar em qualquer um dos grafos para abrir os dados no Metrics Explorer, que é descrito abaixo.

## <a name="view-the-activity-log"></a>Exibir o log de atividades
O log de atividades fornece informações sobre as operações em cada recurso do Azure na assinatura. Isso incluirá informações como quando um recurso for criado ou modificado, quando um trabalho for iniciado ou quando ocorrer uma determinada operação.

1. Na parte superior do menu do recurso, selecione log de **atividades**.
2. O filtro atual é definido como eventos relacionados ao seu recurso. Se você não vir nenhum evento, tente alterar o **período** para aumentar o escopo de tempo.

    ![Registo de atividades](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Se você quiser ver eventos de outros recursos em sua assinatura, altere os critérios no filtro ou até mesmo remova as propriedades do filtro.

    ![Registo de atividades](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Ver métricas
Métricas são valores numéricos que descrevem algum aspecto do recurso em um determinado momento. Azure Monitor coleta automaticamente as métricas de plataforma em intervalos de um minuto de todos os recursos do Azure. Você pode exibir essas métricas usando o Metrics Explorer.

1. Na seção **monitoramento** do menu do recurso, selecione **métricas**. Isso abre o Metrics Explorer com o escopo definido para seu recurso.
2. Clique em **Adicionar métrica** para adicionar uma métrica ao gráfico.
   
   ![Gerenciador de métricas](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecione uma **métrica** na lista suspensa e, em seguida, uma **agregação**. Isso define como os valores coletados serão amostrados em cada intervalo de tempo.

    ![Gerenciador de métricas](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Clique em **Adicionar métrica** para adicionar outras combinações de métrica e agregação ao gráfico.

    ![Gerenciador de métricas](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você exibiu o log de atividades e as métricas de um recurso do Azure que são coletados automaticamente pelo Azure Monitor. Os logs de recursos fornecem informações sobre a operação detalhada do recurso, mas devem ser configurados para serem coletados. Continue no tutorial para coletar logs de recursos em um espaço de trabalho Log Analytics onde eles podem ser analisados usando consultas de log.

> [!div class="nextstepaction"]
> [Coletar e analisar logs de recursos com Azure Monitor](tutorial-resource-logs.md)
