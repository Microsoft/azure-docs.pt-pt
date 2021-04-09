---
title: Construção de soluções para Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicações IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral da construção de soluções integradas com a IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100418235"
---
# <a name="iot-central-solution-builder-guide"></a>Guia de construtor de soluções IoT Central

*Este artigo aplica-se aos construtores de soluções.*

Uma aplicação IoT Central permite monitorizar e gerir milhões de dispositivos ao longo do seu ciclo de vida. Este guia destina-se a construtores de soluções que utilizam a IoT Central para construir soluções integradas. Uma aplicação IoT Central permite-lhe gerir dispositivos, analisar telemetria do dispositivo e integrar-se com outros serviços de back-end.

Um construtor de soluções:

- Configura tabliers e vistas na IoT Central Web UI.
- Utiliza as regras incorporadas e ferramentas de análise para obter insights de negócio dos dispositivos conectados.
- Utiliza as capacidades de exportação de dados e regras para integrar a IoT Central com outros serviços de back-end.

## <a name="configure-dashboards-and-views"></a>Configure painéis e vistas

Uma aplicação IoT Central pode ter um ou mais dashboards que os operadores usam para visualizar e interagir com a aplicação. Como construtor de soluções, pode personalizar o painel de instrumentos padrão e criar dashboards especializados:

- Para ver alguns exemplos de dashboards personalizados, consulte [os modelos focados na Indústria.](concepts-app-templates.md#industry-focused-templates)
- Para saber mais sobre os dashboards, consulte [criar e gerir vários dashboards](howto-create-personal-dashboards.md) e [configurar o painel de aplicações.](howto-add-tiles-to-your-dashboard.md)

Quando um dispositivo se liga a uma Central IoT, o dispositivo está associado a um modelo de dispositivo para o tipo de dispositivo. Um modelo de dispositivo tem vistas personalizáveis que um operador usa para gerir dispositivos individuais. Como desenvolvedor de soluções, pode criar e personalizar as vistas disponíveis para um tipo de dispositivo. Para saber mais, consulte [Adicionar pontos de vista.](howto-set-up-template.md#add-views)

## <a name="use-built-in-rules-and-analytics"></a>Utilize regras e análises incorporadas

Um desenvolvedor de soluções pode adicionar regras a uma aplicação IoT Central que execute ações personalizáveis. As regras avaliam as condições, com base em dados provenientes de um dispositivo, para determinar quando executar uma ação. Para saber mais sobre regras, consulte:

- [Tutorial: Criar uma regra e configurar notificações na sua aplicação do Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Criar ações de webhook em regras no Azure IoT Central](howto-create-webhooks.md)
- [Agrupar múltiplas ações para executar a partir de uma ou mais regras](howto-use-action-groups.md)

A IoT Central tem capacidades de análise incorporadas que um operador pode usar para analisar os dados que fluem dos dispositivos conectados. Para saber mais, consulte [Como utilizar a análise para analisar dados do dispositivo.](howto-create-analytics.md)

## <a name="integrate-with-other-services"></a>Integrar noutros serviços

Como construtor de soluções, pode utilizar as capacidades de exportação de dados e regras na IoT Central para se integrar com outros serviços. Para saber mais, veja:

- [Exportar dados de IoT para destinos em nuvem usando exportação de dados](howto-export-data.md)
- [Utilize fluxos de trabalho para integrar a sua aplicação Azure IoT Central com outros serviços na nuvem](howto-configure-rules-advanced.md)
- [Expandir o Azure IoT Central com regras personalizadas através do Stream Analytics, das Funções do Azure do SendGrid](howto-create-custom-rules.md)
- [Estenda a Azure IoT Central com análises personalizadas usando Azure Databricks](howto-create-custom-analytics.md)
- [Visualize e analise os seus dados Azure IoT Central num painel power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre a utilização da IoT Central, os próximos passos sugeridos são experimentar os quickstarts, começando com [a configuração de uma aplicação Azure IoT Central](./quick-deploy-iot-central.md).
