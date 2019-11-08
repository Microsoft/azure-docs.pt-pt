---
title: Recursos da solução de fábrica conectada – Azure | Microsoft Docs
description: Este artigo descreve uma visão geral dos recursos da solução pré-configurada de fábrica conectada, como painel de nuvem, regras e alertas.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820101"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>O que é o acelerador de solução IoT de fábrica conectada?

A fábrica conectada é uma implementação da arquitetura de referência de IoT industrial do Azure da Microsoft, empacotada como uma solução de software livre. Você pode usá-lo como um ponto de partida para um produto comercial. Você pode implantar uma versão criada previamente da solução de fábrica conectada em sua assinatura do Azure por meio dos [aceleradores de solução do Azure IOT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Painel da solução de fábrica conectada](./media/iot-accelerators-connected-factory-features/dashboard.png)

O código do Solution Accelerator da fábrica conectada [está disponível no GitHub](https://github.com/Azure/azure-iot-connected-factory).

A fábrica conectada inclui os seguintes recursos:

## <a name="industrial-device-interoperability"></a>Interoperabilidade de dispositivos industriais

- Ligue a recursos industriais através de uma interface OPC UA.
- Use as linhas de produção simuladas (executando servidores OPC UA em contêineres do Docker) para ver a telemetria ao vivo.
- Procure o modelo de informações do OPC UA dos servidores OPC UA de um painel de nuvem.

## <a name="remote-management"></a>Gestão remota

- Configure os recursos OPC UA a partir do dashboard na cloud (métodos de chamada, leitura e escrita de dados).
- Publique e anule a publicação de dados telemétricos dos recursos OPC UA a partir de um dashboard na cloud.

## <a name="cloud-dashboard"></a>Dashboard na cloud

- Exibir visualizações de telemetria diretamente em um painel de nuvem.
- Veja as tendências nos dados telemétricos e crie correlações com o dashboard do Explorador do Time Series Insights.
- Consulte OEE (eficiência geral do equipamento) e KPIs (indicadores chave de desempenho) calculados em um painel de nuvem.
- Exiba hierarquias de ativos industriais em uma topologia de árvore, bem como em um mapa interativo.
- Exibir, reconhecer e fechar alertas de um painel de nuvem.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series insights](../time-series-insights/time-series-insights-overview.md) é criado para armazenar, Visualizar e consultar grandes quantidades de dados de série temporal. A fábrica conectada aproveita esse serviço.
- A fábrica conectada integra-se com esse serviço, permitindo que você execute análises profundas e em tempo real dos dados do seu dispositivo.

## <a name="rules-and-alerts"></a>Regras e alertas

[Configure regras baseadas em limite para alertas](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Segurança ponto a ponto

- Configure permissões de segurança para utilizadores através do Controlo de Acesso Baseado em Funções (RBAC).
- A criptografia de ponta a ponta é implementada usando a autenticação OPC UA (usando certificados X. 509), bem como tokens de segurança.

## <a name="customizability"></a>Personalização

- Personalize a solução para atender a requisitos específicos de negócios.
- Fonte de solução completa-código disponível no GitHub. Consulte o repositório de [solução pré-configurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Solution Accelerator da fábrica conectada, consulte o início rápido [Experimente uma solução baseada em nuvem para gerenciar meus dispositivos IOT industriais](quickstart-connected-factory-deploy.md).
