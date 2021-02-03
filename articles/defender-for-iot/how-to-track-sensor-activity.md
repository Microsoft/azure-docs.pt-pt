---
title: Controlar a atividade do sensor
description: A linha temporal do evento apresenta uma linha temporal de atividade detetada na sua rede, incluindo alertas e ações de gestão de alerta, eventos de rede e operações de utilizador, como o registo do utilizador e a eliminação do utilizador.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6dbe15da85a85303742ef28c7ca609755193f25e
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509104"
---
# <a name="track-sensor-activity"></a>Controlar a atividade do sensor

## <a name="event-timeline"></a>Cronologia do evento

A linha temporal do evento apresenta uma linha temporal de atividade que o seu sensor detetou. Por exemplo:

  - Alertas e ações de gestão de alerta

  - Eventos de rede

  - Operações do utilizador, tais como o pedido de insuflação do utilizador e a eliminação do utilizador

A cronologia do evento proporciona uma visão cronológica dos acontecimentos que ocorreram na rede. A cronologia do evento permite compreender e analisar a cadeia de eventos que precedeu e acompanhou um ataque ou incidente, que ajuda na investigação e forenses.

> [!NOTE]
> *Administradores* e *analistas de segurança* podem executar os procedimentos descritos nesta secção.

Para ver os registos do evento:

- A partir do menu lateral, selecione **Event Timeline**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Veja os seus eventos na cronologia do evento.":::

Além de visualizar os eventos que o sensor detetou, pode adicionar manualmente eventos à linha do tempo. Este processo é útil se o evento aconteceu num sistema externo mas tem um impacto na sua rede, e é importante gravar o evento e apresentá-lo como parte da linha temporal.

Para adicionar eventos manualmente:

- Selecione **Criar Evento**.

Para exportar informações de registo de eventos num ficheiro CSV:

- Selecione **Export** (Exportar).

## <a name="filter-the-event-timeline"></a>Filtrar a cronologia do evento

Filtrar a linha do tempo para exibir dispositivos e eventos de interesse para si.

Para filtrar a linha do tempo:

1. Selecione **Filtros Avançados**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Utilize a janela De Filtros Avançados para filtrar os seus eventos.":::

2. Definir filtros de eventos, da seguinte forma:

   - **Incluir Endereço**: Mostrar eventos para dispositivos específicos.

   - **Excluir endereço**: Ocultar eventos para dispositivos específicos.

   - **Incluir tipos de eventos**: Exibir tipos de eventos específicos.

   - **Excluir tipos de eventos**: Ocultar tipos de eventos específicos.

   - **Grupo de Dispositivos**: Selecione um grupo de dispositivos, tal como foi definido no mapa do dispositivo. Apenas os eventos deste grupo são apresentados.

3. Selecione **Clear All** para limpar todos os filtros selecionados.

4. Pesquisar por **Alertas Apenas,** **Alertas e Avisos,** ou **Todos os Eventos**.

5. **Selecione 'Selecionar's date** para escolher uma data específica. Escolha um dia, hora e minuto. Os eventos do prazo selecionado são mostrados.

6.  Selecione **Operações de Utilizador** para incluir ou excluir eventos de operação do utilizador.

7.  Selecione a seta **(V)** para ver mais informações sobre o evento:

    - Selecione os alertas relacionados (se houver) para apresentar uma descrição detalhada do alerta.

    - Selecione o dispositivo para visualizar o dispositivo no mapa.

    - **Selecione eventos de filtragem por dispositivos relacionados** se pretender filtrar por dispositivos relacionados.

    - Selecione **O Ficheiro PCAP** para descarregar o ficheiro PCAP (se existir) que contém uma captura de pacote de toda a rede num momento específico. 
    
      O ficheiro PCAP contém informações técnicas que podem ajudar os engenheiros de rede a determinar os parâmetros exatos do evento. Pode analisar o ficheiro PCAP com um analisador de protocolo de rede, como o Wireshark, uma aplicação de código aberto.

## <a name="see-also"></a>Ver também

[Ver alertas](how-to-view-alerts.md)
