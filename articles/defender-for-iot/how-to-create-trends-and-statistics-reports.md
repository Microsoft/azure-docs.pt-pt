---
title: Gerar tendências e relatórios estatísticos
description: Obtenha informações sobre a atividade da rede, estatísticas e tendências utilizando widgets defender para tendências e estatísticas de IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c28e2f1c24d39ceb915be9f4f6f222d70de9ee73
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522228"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendências dos sensores e relatórios estatísticos

## <a name="about-sensor-trends-and-statistics-reports"></a>Sobre tendências de sensores e relatórios estatísticos

Você pode criar gráficos de widget e gráficos de tortas para obter informações sobre as tendências e estatísticas da rede. Os widgets podem ser agrupados em dashboards definidos pelo utilizador.

> [!NOTE]
> Os analistas de administrador e de segurança podem criar relatórios de Tendências e Estatísticas.

O painel é composto por widgets que descrevem graficamente os seguintes tipos de informação:

- Tráfego por porto
- Tráfego superior por porto
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Largura de banda de topo por VLAN
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por SISTEMA
  - Número de dispositivos por VLAN
  - Dispositivos desligados
- Conectividade cai por horas
- Alertas para incidentes por tipo
- Acesso à tabela de bases de dados
- Widgets de dissecação de protocolo
- DELTAV
  - Distribuição de operações deltaV roc
  - Eventos deltav roc pelo nome
  - Eventos DeltaV por tempo
- AMS
  - Tráfego AMS por porta de servidor
  - Tráfego AMS por comando
- Endereço Ethernet e IP:
  - Tráfego de endereços Ethernet e IP pelo serviço CIP
  - Tráfego de endereços Ethernet e IP por classe CIP
  - Tráfego de endereços Ethernet e IP por comando
- OPC:
  - Operações de gestão de topo da OPC
  - Operações de E/S de topo da OPC
- Siemens S7:
  - Tráfego S7 por função de controlo
  - Tráfego S7 por subfunção
- Rio VLAN
  - Número de dispositivos por VLAN
  - Largura de banda superior por VLAN
- 60870-5-104
  - Tráfego IEC-60870 pela ASDU
- BACNET
  - Serviços BACnet
- DNP3
  - Tráfego DNP3 por função
- SRTP:
  - Tráfego SRTP por código de serviço
  - Erros SRTP de dia
- SuiteLink:
  - Etiquetas de topo suiteLink
  - Comportamento da etiqueta numérica SuiteLink
- Tráfego IEC-60870 pela ASDU
- Tráfego DNP3 por função
- Tráfego MMS por serviço
- Modbus tráfego por função
- Tráfego OPC-UA por serviço

> [!NOTE]
>  O tempo nos widgets é definido de acordo com o tempo do sensor.

## <a name="create-reports"></a>Criar relatórios

Para ver painéis e widgets:

Selecione **Tendências & Estatísticas** no menu lateral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot de uma investigação.":::

Por predefinição, os resultados são apresentados para deteções ao longo dos últimos 7 dias. Pode utilizar ferramentas de filtro para alterar esta gama. Por exemplo, a pesquisa por texto não pode ser enviado.

## <a name="next-steps"></a>Passos seguintes

[Relatórios de avaliação de](how-to-create-risk-assessment-reports.md) 
 riscos [Consultas de mineração de dados de sensores](how-to-create-data-mining-queries.md) 
 [Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)