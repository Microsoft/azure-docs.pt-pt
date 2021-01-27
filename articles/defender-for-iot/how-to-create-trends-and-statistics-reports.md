---
title: Gerar tendências e relatórios estatísticos
description: Obtenha informações sobre a atividade da rede, estatísticas e tendências utilizando widgets defender para tendências e estatísticas de IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811668"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendências dos sensores e relatórios estatísticos

## <a name="about-sensor-trends-and-statistics-reports"></a>Sobre tendências de sensores e relatórios estatísticos

Você pode criar gráficos de widget e gráficos de tortas para obter informações sobre as tendências e estatísticas da rede. Os widgets podem ser agrupados em dashboards definidos pelo utilizador.

> [!NOTE]
> Os analistas de administrador e de segurança podem criar relatórios de Tendências e Estatísticas.

O painel é composto por widgets que descrevem graficamente os seguintes tipos de informação:

- Tráfego por porto
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por SISTEMA
  - Dispositivos desligados
- Conectividade gota por horas
- Alertas para incidentes por tipo
- Acesso à tabela de bases de dados
- Widgets de dissecação de protocolo
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

## <a name="see-also"></a>Veja também

[Relatórios de avaliação de](how-to-create-risk-assessment-reports.md) 
 riscos [Consultas de mineração de dados de sensores](how-to-create-data-mining-queries.md) 
 [Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)