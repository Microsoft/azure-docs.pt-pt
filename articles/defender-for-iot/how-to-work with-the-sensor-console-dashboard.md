---
title: Trabalhe com o painel de consolas sensorial
description: O painel de instrumentos permite-lhe visualizar rapidamente o estado de segurança da sua rede. Fornece uma visão geral de alto nível das ameaças a todo o seu sistema numa linha de tempo, juntamente com informações sobre dispositivos relacionados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c9afc22cd123a782c9ee0247952c78c72ee916c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523639"
---
# <a name="the-dashboard"></a>O dashboard

O painel de instrumentos permite-lhe visualizar rapidamente o estado de segurança da sua rede. Fornece uma visão geral de alto nível das ameaças a todo o seu sistema numa linha temporal, juntamente com informações sobre dispositivos relacionados, incluindo:

- Alertas em diferentes níveis de gravidade:

- Crítico

- Maior

- Menor

- Avisos

- Os dois indicadores no centro da página mostram os Pacotes por Segundo (PPS) e alertas não reconhecidos (UA). **PPS** é o número de pacotes reconhecidos pelo sistema por segundo. **UA** é o número de alertas que ainda não foram reconhecidos.

- Lista de alertas não reconhecidos com a sua descrição.

- Linha do tempo com a descrição do alerta.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Dashboard":::

## <a name="viewing-the-latest-alerts"></a>Ver os últimos alertas

O medidor de Alertas Não Reconhecidos (UA) no centro da página indica o número de tais alertas. Para ver uma lista de alertas, selecione **Mais Alertas** na parte inferior da página do painel de instrumentos ou selecione **Alertas** no menu lateral.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Alertas não reconhecidos":::

## <a name="status-boxes"></a>Caixas de estado

Cada caixa de estado é descrita nesta secção.

| Caixa de Estado e Medidores | Description |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Alertas Críticos"::: | **Alertas Críticos** - a caixa no meio superior da página indica o número de alertas críticos. Selecione esta caixa para apresentar descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Alertas Principais"::: | **Alertas Principais** - a caixa no topo direito da página indica o número de alertas principais. Selecione esta caixa para apresentar descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Pequenos Alertas"::: | **Alertas Menores** - a caixa na parte inferior esquerda da página indica o número de alertas menores. Selecione esta caixa para apresentar descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Alertas de Aviso"::: | **Alertas de aviso** - a caixa no meio inferior da página indica o número de alertas de aviso. Selecione esta caixa para apresentar descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Todos os Alertas** - a caixa no canto inferior direito da página indica o número total de alertas críticos, principais, menores e de aviso. Selecione esta caixa para apresentar descrições dos alertas na linha do tempo e na lista sob os medidores, se houver. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Pacotes por segundo"::: | **Pacotes Por Segundo (PPS)** - a métrica PPS é um indicador do desempenho da rede. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Eventos Não Reconhecidos (UA)"::: | **Eventos não reconhecidos** - esta métrica indica o número de eventos não reconhecidos.

## <a name="using-the-timeline"></a>Usando a linha do tempo

Os alertas são apresentados ao longo de uma linha temporal vertical que inclui informações de data e hora.

A Linha do Tempo apresenta graficamente:

- Alertas Críticos

- Alertas Principais

- Pequenos Alertas

- Alertas de Aviso

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Gráfico da linha do tempo":::

## <a name="viewing-alerts"></a>Alertas de visualização

Selecione a seta **V** para baixo na parte inferior de uma caixa de alerta para visualizar a entrada de alerta e informações dos dispositivos.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Informações de entrada de alerta e dispositivos":::

- Selecione o dispositivo para visualizar o mapa do modo físico. Os dispositivos sujeitos são destacados.

- Clique em qualquer lugar na caixa de alerta para mostrar detalhes adicionais sobre o alerta. Um popup será exibido semelhante ao abaixo

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: para exportar um ficheiro CSV sobre o alerta.

- Apenas administradores e analistas de segurança — Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="Reconheça todos"::: para **reconhecer todos os** alertas associados.

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::para descarregar um relatório de alerta como um ficheiro PDF.

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="Pin":::para fixar ou desapinar o alerta. A seleção do pin irá adicioná-lo à janela **alertas fixados** no ecrã **alertas.**

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Baixar"::: para investigar o alerta descarregando o ficheiro PCAP relacionado contendo uma análise de protocolo de rede.

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="Cloud"::: para descarregar um ficheiro PCAP filtrado relacionado que contém apenas os pacotes relevantes para o alerta, reduzindo assim o tamanho do ficheiro de saída e permitindo uma análise mais focada. Pode vê-lo usando [o Wireshark.](https://www.wireshark.org/)

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Navegação"::: para navegar na linha do tempo do evento no momento do alerta solicitado. Isto permite-lhe avaliar outros eventos que possam estar a acontecer em torno do alerta específico.

- Administradores e analistas de segurança - altere o estado do alerta de não reconhecido para reconhecido. Selecione Aprenda a aprovar a atividade detetada.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Conectividade não autorizada na Internet detetada":::

## <a name="see-also"></a>Ver também

[Trabalhe com alertas no seu sensor](how-to-work-with-alerts-on-your-sensor.md)
