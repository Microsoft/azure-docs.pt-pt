---
title: Funcionalidades de solução Connected Factory - Azure / Microsoft Docs
description: Este artigo descreve uma visão geral das características da solução pré-configurada da Fábrica Conectada, tais como painel de instrumentos de nuvem, regras e alertas.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820101"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>O que é acelerador de soluções IoT da Fábrica Conectada?

A Connected Factory é uma implementação da arquitetura de referência Azure Industrial IoT da Microsoft, embalada como em solução de código aberto. Pode usá-lo como ponto de partida para um produto comercial. Pode implantar uma versão pré-construída da solução Connected Factory na sua subscrição Azure a partir de aceleradores de [soluções Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Painel de solução de fábrica conectada](./media/iot-accelerators-connected-factory-features/dashboard.png)

O código acelerador de soluções Connected Factory [está disponível no GitHub](https://github.com/Azure/azure-iot-connected-factory).

A Fábrica Conectada inclui as seguintes funcionalidades:

## <a name="industrial-device-interoperability"></a>Interoperabilidade do dispositivo industrial

- Ligue-se a ativos industriais com uma interface OPC UA.
- Utilize as linhas de produção simuladas (servidores OPC UA em contentores Docker) para ver telemetria ao vivo a partir deles.
- Navegue no modelo de informação OPC UA dos servidores DaUA oPC a partir de um dashboard em nuvem.

## <a name="remote-management"></a>Gestão remota

- Configure os seus ativos da OPC UA a partir do painel de dados da nuvem (métodos de chamada, leia e escreva dados).
- Publique e não publique dados de telemetria dos seus ativos da OPC UA a partir de um dashboard em nuvem.

## <a name="cloud-dashboard"></a>Painel de dados de nuvem

- Veja as pré-visualizações de telemetria diretamente num painel de instrumentos de nuvem.
- Veja as tendências dos dados de telemetria e crie correlações utilizando o dashboard Time Series Insights Explorer.
- Consulte a eficiência global do equipamento calculado (OEE) e os indicadores de desempenho chave (KPIs) a partir de um painel de instrumentos de nuvem.
- Veja as hierarquias de ativos industriais numa topologia de árvores, bem como num mapa interativo.
- Veja, reconheça e feche os alertas de um painel de instrumentos de nuvem.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [A Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) foi construída para armazenar, visualizar e consultar grandes quantidades de dados da série time. A Connected Factory aproveita este serviço.
- A Connected Factory integra-se com este serviço permitindo-lhe realizar uma análise profunda e em tempo real dos dados do seu dispositivo.

## <a name="rules-and-alerts"></a>Regras e alertas

[Configure regras baseadas em limiares para alertas](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Segurança de ponta a ponta

- Configure permissões de segurança para utilizadores que utilizem controlo de acesso baseado em funções (RBAC).
- A encriptação de ponta a ponta é implementada utilizando a autenticação OPC UA (utilizando certificados X.509) bem como tokens de segurança.

## <a name="customizability"></a>Personalizabilidade

- Personalize a solução para atender aos requisitos comerciais específicos.
- Código fonte de solução completo disponível no GitHub. Consulte o repositório de [solução pré-configurada da Fábrica Conectada.](https://github.com/Azure/azure-iot-connected-factory)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o acelerador de soluções Connected Factory, consulte o Quickstart Experimente uma solução baseada na nuvem para gerir os [meus dispositivos IoT industriais](quickstart-connected-factory-deploy.md).
