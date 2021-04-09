---
title: Conheça os dispositivos em zonas específicas
description: Utilize a consola de gestão no local para obter uma informação de visualização completa por zona específica
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776337"
---
# <a name="view-information-per-zone"></a>Ver informações por zona


## <a name="view-a-device-map-for-a-zone"></a>Ver um mapa de dispositivo para uma zona

Veja um mapa do dispositivo para uma zona selecionada num sensor. Esta visualização apresenta todos os elementos de rede relacionados com a zona selecionada, incluindo os sensores, os dispositivos ligados a eles e outras informações.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Screenshot do mapa da zona.":::


- Na janela **'Gestão** do Site', selecione **'Ver Mapa'** da barra que contém o nome da zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Região padrão para unidade de negócio padrão.":::

A janela **do mapa do dispositivo** aparece. As seguintes ferramentas estão disponíveis para visualizar dispositivos e informações do dispositivo a partir do mapa. Para obter mais informações sobre cada uma destas funcionalidades, consulte o *guia do utilizador da plataforma Defender para IoT*.

- **Vistas de zoom do** mapa : Vista simplificada, vista de conexões e vista detalhada. A vista do mapa apresentado varia consoante o nível de zoom do mapa. Alterna entre as vistas do mapa ajustando os níveis de zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Ferramentas de pesquisa e layout** de mapas : Ferramentas utilizadas para exibir segmentos de rede variados, dispositivos, grupos de dispositivos ou camadas.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Screenshot da vista Ferramentas de Procura e Layout.":::

- **Etiquetas e indicadores nos dispositivos:** Por exemplo, o número de dispositivos agrupados numa sub-rede numa rede de TI. Neste exemplo, são 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Screenshot de etiquetas e indicadores.":::

- **Ver as propriedades** do dispositivo : Por exemplo, o sensor que está a monitorizar o dispositivo e as propriedades básicas do dispositivo. Clique com o botão direito para ver as propriedades do dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Screenshot da vista propriedades do dispositivo.":::

- **Alerta associado a um dispositivo:** Clique com o botão direito no dispositivo para visualizar alertas relacionados.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Screenshot da vista 'Show Alerts'.":::

## <a name="view-alerts-associated-with-a-zone"></a>Ver alertas associados a uma zona

Para visualizar alertas associados a uma zona específica:

- Selecione o ícone de alerta formulário a janela **Zona.** 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="A vista padrão da Unidade de Negócio com exemplos.":::

Para mais informações, consulte [a Visão Geral: Trabalhar com alertas.](how-to-work-with-alerts-on-premises-management-console.md)

### <a name="view-the-device-inventory-of-a-zone"></a>Ver o inventário do dispositivo de uma zona

Para visualizar o inventário do dispositivo associado a uma zona específica:

- Selecione O Inventário do Dispositivo de **Visualização** a partir da janela **Zona.**

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Aparecerá o ecrã de inventário do dispositivo.":::

Para obter mais informações, consulte [investigar todas as deteções de sensores empresariais num inventário de dispositivos.](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

## <a name="view-additional-zone-information"></a>Ver informações adicionais sobre zona

Estão disponíveis as seguintes informações adicionais sobre zonas:

- **Detalhes da zona**: Ver o número de dispositivos, alertas e sensores associados à zona.

- **Detalhes do sensor**: Ver o nome, endereço IP e versão de cada sensor atribuído à zona.

- **Estado da conectividade**: Se um sensor estiver desligado, ligue-o do sensor. Consulte [os sensores de ligação à consola de gestão no local.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console) 

- **Atualização do progresso**: Se o sensor ligado estiver a ser atualizado, aparecerão os estados de atualização. Durante a atualização, a consola de gestão no local não recebe informações do dispositivo do sensor.

## <a name="next-steps"></a>Passos seguintes

[Obter informações sobre ameaças globais, regionais e locais](how-to-gain-insight-into-global-regional-and-local-threats.md)
