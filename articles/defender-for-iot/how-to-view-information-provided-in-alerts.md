---
title: Ver informações em alertas
description: Selecione um alerta da janela Alertas para rever detalhes.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5877db6b3bc7366f28e679882a2c784e6828b1c1
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523588"
---
# <a name="view-information-in-alerts"></a>Ver informações em alertas

Selecione um alerta na janela **Alertas** para rever os detalhes do alerta. Os detalhes incluem as seguintes informações:

- Metadados de alerta

- Informação sobre tráfego, dispositivos e o evento

- Ligações a dispositivos conectados no mapa do dispositivo

- Comentários definidos por analistas e administradores de segurança

- Recomendações para investigar o evento

## <a name="alert-metadata"></a>Metadados de alerta

Os detalhes do alerta incluem os seguintes metadados de alerta:

  - ID de alerta

  - Motor de política que desencadeou o alerta

  - Data e hora em que o alerta foi desencadeado

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Conectividade não autorizada na Internet detetada.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informação sobre dispositivos, tráfego e o evento

A mensagem de alerta fornece informações sobre:

  - Os dispositivos detetados.

  - O tráfego detetado entre os dispositivos, tais como protocolos e códigos de função.

  - Insights sobre as implicações do evento.

Pode utilizar esta informação ao decidir como gerir o evento de alerta.

## <a name="links-to-connected-devices-in-the-device-map"></a>Ligações a dispositivos conectados no mapa do dispositivo

Para saber mais sobre dispositivos ligados aos dispositivos detetados, pode selecionar uma imagem do dispositivo no alerta e visualizar dispositivos conectados no mapa.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="A operação RCP falhou.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Imagem de imagem dos dispositivos.":::

O mapa filtra o dispositivo que selecionou e outros dispositivos ligados ao mesmo. O mapa também apresenta a caixa de diálogo **Quick Properties** para os dispositivos detetados nos alertas.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Comentários definidos por analistas e administradores de segurança 

Os alertas podem incluir uma lista de comentários predefinidos. Por exemplo, os comentários podem ser instruções para ações de mitigação a tomar, ou nomes de indivíduos para contactar sobre o evento.

Quando estiver a gerir um evento de alerta, pode escolher o comentário ou comentários que melhor reflitam o estado do evento ou os passos que tomou para investigar o alerta.

Os comentários selecionados são guardados na mensagem de alerta. Trabalhar com comentários melhora a comunicação entre indivíduos e equipas durante a investigação de um evento de alerta. Como resultado, os comentários podem acelerar o tempo de resposta a incidentes.

Um administrador ou analista de segurança predefine comentários. Os comentários selecionados não são reencaminhados para sistemas parceiros definidos nas regras de encaminhamento.

Depois de rever as informações em alerta, pode realizar várias etapas forenses para guiá-lo na gestão do evento de alerta. Por exemplo:

- Analise a atividade recente do dispositivo (relatório de mineração de dados). 

- Analise outros eventos que ocorreram ao mesmo tempo (cronograma do evento). 

- Analise o tráfego de eventos completo (ficheiro PCAP).

## <a name="pcap-files"></a>Ficheiros PCAP

Em alguns casos, pode aceder a um ficheiro PCAP a partir da mensagem de alerta. Isto pode ser útil se quiser informações mais detalhadas sobre o tráfego de rede associado.

Para descarregar um ficheiro PCAP, selecione :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="Baixar o ícone."::: no canto superior direito da caixa de diálogo **de detalhes alerta.**

## <a name="recommendations-for-investigating-an-event"></a>Recomendações para investigar um evento 

A área **de Recomendação** de um alerta exibe informações que podem ajudá-lo a entender melhor um evento. Reveja estas informações antes de gerir o evento de alerta ou tomar medidas no dispositivo ou na rede.

## <a name="next-steps"></a>Passos seguintes

[Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)

[Gerir o evento de alerta](how-to-manage-the-alert-event.md)
