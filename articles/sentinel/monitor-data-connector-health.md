---
title: Monitorize a saúde dos seus conectores de dados com este livro do Azure Sentinel Microsoft Docs
description: Utilize o manual de monitorização da saúde para acompanhar a conectividade e o desempenho dos seus conectores de dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656995"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Monitorize a saúde dos seus conectores de dados com este livro do Azure Sentinel

O manual de **monitorização de saúde dos conectores de dados** permite-lhe acompanhar a saúde, conectividade e desempenho dos seus conectores de dados, a partir de Azure Sentinel. O livro fornece monitores adicionais, deteta anomalias e dá uma visão sobre o estado de ingestão de dados do espaço de trabalho. Pode utilizar a lógica do livro para monitorizar a saúde geral dos dados ingeridos e construir vistas personalizadas e alertas baseados em regras.

## <a name="use-the-health-monitoring-workbook"></a>Use o livro de monitorização da saúde

1. A partir do portal Azure Sentinel, selecione Livros de **Trabalho** do menu de **gestão de Ameaças.**

1. Na galeria **Workbooks, insira** a *saúde* na barra de pesquisa e selecione **a monitorização** de saúde da recolha de dados entre os resultados.

1. Selecione **o modelo de ver** para usar o livro como está ou selecione **Guardar** para criar uma cópia editável do livro. Quando a cópia for criada, **selecione Ver livro guardado**.

1. Uma vez no livro, selecione primeiro a **subscrição** e o **espaço de trabalho** que deseja visualizar e, em seguida, defina o **TimeRange** para filtrar os dados de acordo com as suas necessidades. Utilize a ajuda do **Show** para mostrar a explicação no local do livro.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="página de desembarque de trabalho de monitorização de saúde do conector de dados" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Há três secções com açapões neste livro:

1. O **separador Visão Geral** mostra o estado geral da ingestão de dados no espaço de trabalho selecionado: medidas de volume, taxas EPS e tempo do último registo recebido.

1. O **separador de anomalias de recolha de dados** irá ajudá-lo a detetar anomalias no processo de recolha de dados, por tabela e fonte de dados. Cada separador apresenta anomalias para uma tabela específica (o separador **Geral** inclui uma coleção de tabelas). As anomalias são calculadas utilizando a função **series_decompose_anomalies()** que devolve uma **pontuação de anomalia**. [Saiba mais sobre esta função](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Desaguise os seguintes parâmetros para a função avaliar:

    - **AnomaliesTimeRange**: Este seletor de hora só se aplica à vista de anomalias da recolha de dados.
    - **SampleInterval**: O intervalo de tempo no qual os dados são amostrados no período de tempo especificado. A classificação de anomalias só é calculada para os dados do último intervalo.
    - **PositiveAlertThreshold**: Este valor define o limiar de classificação de anomalias positivo. Aceita valores decimais.
    - **NegativeAlertThreshold**: Este valor define o limiar de classificação de anomalias negativo. Aceita valores decimais.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="página de anomalias de monitorização de saúde do conector de dados" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. O separador **informações do Agente** mostra-lhe informações sobre a saúde dos agentes do Log Analytics instalados nas suas várias máquinas, sejam a Azure VM, outros VM em nuvem, VM no local ou físico. Pode monitorizar o seguinte:

   - Localização do sistema

   - Estado do batimento cardíaco e latência

   - Espaço disponível de memória e disco

   - Operações de agente

    Nesta secção deve selecionar o separador que descreve o ambiente das suas máquinas: escolha o separador **máquinas geridos pelo Azure** se quiser ver apenas as máquinas geridas pelo Arco Azure; escolha o separador **Todas as máquinas** para visualizar máquinas geridas e não-Azure com o agente Log Analytics instalado.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="página de informações de agente de monitorização de saúde do conector de dados" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Próximos passos
Saiba como [embarcar os seus dados no Azure Sentinel,](quickstart-onboard.md)ligue [fontes de dados](connect-data-sources.md)e [obtenha visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)