---
title: Características da solução Connected Factory - Azure / Microsoft Docs
description: Este artigo descreve uma visão geral das características da solução pré-configurada da Fábrica Conectada, como painel de contas, regras e alertas.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 3a294368e82bcd00f98c26504b3141e8f39fe0c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261605"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>O que é o acelerador de solução IoT da fábrica?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Connected Factory é uma implementação da arquitetura de referência Azure Industrial IoT da Microsoft, embalada como solução de código aberto. Pode usá-lo como ponto de partida para um produto comercial. Pode implementar uma versão pré-construída da solução Connected Factory na sua subscrição Azure a partir de aceleradores de [solução Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Painel de solução de fábrica conectado](./media/iot-accelerators-connected-factory-features/dashboard.png)

O código acelerador de solução Connected Factory [está disponível no GitHub](https://github.com/Azure/azure-iot-connected-factory).

A Fábrica Conectada inclui as seguintes características:

## <a name="industrial-device-interoperability"></a>Interoperabilidade do dispositivo industrial

- Conecte-se a ativos industriais com uma interface OPC UA.
- Utilize as linhas de produção simuladas (executando servidores OPC UA em contentores Docker) para ver telemetria ao vivo a partir deles.
- Navegue no modelo de informação UA OPC dos servidores OPC UA a partir de um painel de instrumentos de nuvem.

## <a name="remote-management"></a>Gestão remota

- Configure os seus ativos OPC UA a partir do painel de instrumentos de nuvem (métodos de chamada, leia e escreva dados).
- Publique e não publique dados de telemetria dos seus ativos da OPC UA a partir de um dashboard de nuvem.

## <a name="cloud-dashboard"></a>Painel de nuvem

- Ver pré-visualizações de telemetria diretamente num painel de instrumentos de nuvem.
- Ver tendências nos dados de telemetria e criar correlações utilizando o painel de insights do Time Series Insights Explorer.
- Consulte indicadores de eficiência geral do equipamento calculados (OEE) e principais indicadores de desempenho (KPI's) a partir de um painel de instrumentos de nuvem.
- Veja as hierarquias de ativos industriais numa topologia de árvores, bem como num mapa interativo.
- Ver, reconhecer e fechar alertas de um painel de instrumentos de nuvem.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) é construído para armazenar, visualizar e consultar grandes quantidades de dados de séries de tempo. A Connected Factory aproveita este serviço.
- A Connected Factory integra-se com este serviço permitindo-lhe realizar uma análise profunda e em tempo real dos dados do seu dispositivo.

## <a name="rules-and-alerts"></a>Regras e alertas

[Configure as regras baseadas em limiares para alertas](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Segurança de ponta a ponta

- Configure permissões de segurança para utilizadores que utilizem Role-Based Controlo de Acesso (RBAC).
- A encriptação de ponta a ponta é implementada utilizando a autenticação OPC UA (utilizando certificados X.509) bem como fichas de segurança.

## <a name="customizability"></a>Personalização

- Personalize a solução para satisfazer requisitos específicos de negócio.
- Código-fonte de solução completa disponível no GitHub. Consulte o repositório [de solução pré-configurado da Fábrica Conectada.](https://github.com/Azure/azure-iot-connected-factory)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o acelerador de soluções Connected Factory, consulte o Quickstart [Experimente uma solução baseada na nuvem para gerir os meus dispositivos IoT industriais.](quickstart-connected-factory-deploy.md)
