---
title: Filtrar e gerir alertas da página Alertas
description: Veja os alertas de acordo com várias categorias e utiliza funcionalidades de pesquisa para ajudá-lo a encontrar alertas de interesse.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/22/2021
ms.topic: how-to
ms.openlocfilehash: 391d1872124c7332bdaa6008a244490b47df4bf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781590"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>Filtrar e gerir alertas da página Alertas 

Este artigo descreve como visualizar alertas desencadeados pelo seu sensor e geri-los com ferramentas de alerta.

Pode ver alertas com base em várias categorias, tais como alertas que tenham sido arquivados ou presos. Também pode pesquisar alertas de interesse, tais como alertas baseados num endereço IP ou MAC.  

Também pode ver alertas do painel de instrumentos do sensor.

Para ver alertas:

- Selecione **Alertas** do menu lateral. A janela Alertas apresenta os alertas detetados pelo seu sensor.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Vista do ecrã Alertas.":::

## <a name="view-alerts-by-category"></a>Ver alertas por categoria

Pode ver alertas de acordo com várias categorias a partir da vista principal **alerta.** Selecione um alerta para rever detalhes e gerir o evento.

| Ordenar por tipo | Description |
|--|--|
| **Alertas Importantes** | Alertas ordenados por importância. |
| **Alertas Pinned** | Alertas que o utilizador prendeu para mais investigação. Os alertas fixos não são arquivados e são armazenados durante 14 dias na pasta fixada. |
| **Alertas Recentes** | Alertas ordenados pelo tempo. |
| **Alertas Reconhecidos** | Alertas que foram reconhecidos e despreocupados, ou que foram silenciados e sem mutilação. |
| **Alertas arquivados** | Alerta que o sistema se arquivou automaticamente. Apenas o utilizador administrador pode aceder-lhes. |

## <a name="search-for-alerts-of-interest"></a>Pesquisa de alertas de interesse

A vista principal dos Alertas fornece várias funcionalidades de pesquisa para ajudá-lo a encontrar alertas de interesse.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Alertas a aprender imagens.":::

### <a name="text-search"></a>Pesquisa de texto

Utilize a opção 'Pesquisa Gratuita' para procurar alertas por texto, números ou caracteres.

Para pesquisar:

- Digite o texto necessário no campo 'Procurar' livre e prima Enter no seu teclado.

Para limpar a procura:

- Elimine o texto no campo 'Procurar' livre e prima Enter no seu teclado.

### <a name="device-group-or-device-ip-address-search"></a>Pesquisa de endereços IP de grupo de dispositivo ou dispositivo

Procure alertas que mencionem endereços IP específicos ou grupos de dispositivos. Os grupos de dispositivos são criados no mapa do dispositivo.

Para utilizar filtros avançados:

1. Selecione **Filtros Avançados** a partir da vista principal **dos Alertas.**

2. Escolha um grupo de dispositivos ou um dispositivo.

3. **Selecione Confirmar**.

4. Para limpar a pesquisa, selecione **Clear All**.

### <a name="security-versus-operational-alert-search"></a>Segurança versus busca de alerta operacional

Altere entre os alertas operacionais e de segurança:

- **Os** alertas de segurança representam potenciais tráfego de malware, anomalias na rede, violações de políticas e violações de protocolos.

- Os alertas **operacionais** representam anomalias na rede, violações de políticas e violações de protocolos.

Quando nenhuma das opções é selecionada, todos os alertas são apresentados.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Segurança no ecrã dos alertas.":::

## <a name="alert-page-options"></a>Opções de página de alerta

As mensagens de alerta fornecem as seguintes ações:

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: para reconhecer um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: para não conhecer um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: para fixar um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: para desapinar um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: para reconhecer todos os alertas.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: para aprender e reconhecer todos os alertas.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: para exportar informações de alerta para um ficheiro .csv. Utilize a opção **de exportação de alerta prolongado** para exportar informações de alerta em linhas separadas para cada alerta que cubra vários dispositivos.

## <a name="alert-pop-up-window-options"></a>Opções de janela pop-up de alerta

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: ícone para descarregar um relatório de alerta como um ficheiro PDF.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: ícone para descarregar o ficheiro PCAP. O ficheiro é visível com o Wireshark, o analisador de protocolo de rede gratuito.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: para descarregar um ficheiro PCAP filtrado que contenha apenas os pacotes de alerta relevantes, reduzindo assim o tamanho do ficheiro de saída e permitindo uma análise mais focada. Pode ver o ficheiro utilizando o Wireshark.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: ícone para mostrar o alerta na linha do tempo do evento.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: ícone para fixar o alerta.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: ícone para desagrear o alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: para aprovar o tráfego (apenas analistas de segurança e administradores).

- Selecione um dispositivo para exibi-lo no mapa do dispositivo.

## <a name="next-steps"></a>Passos seguintes

[Gerir o evento de alerta](how-to-manage-the-alert-event.md)

[Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)
