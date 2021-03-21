---
title: Configure Azure Defender para solução baseada em agente IoT
description: Saiba como configurar a recolha de dados no Azure Defender para solução baseada em agentes IoT
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e32b9c690db70baeda7dec2f9f1270f82c6881bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733300"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Configure Azure Defender para solução baseada em agente IoT  

Este artigo descreve como configurar a recolha de dados no Azure Defender para solução baseada em agentes IoT.

## <a name="configure-data-collection"></a>Configurar a recolha de dados

Para configurar a recolha de dados na Solução baseada em agentes IoT: 

1. Navegue para o portal Azure e selecione o Hub IoT a que o Defender para IoT está ligado 

1. No  ****   menu Segurança, selecione **Definições**. 

1. Selecione **a recolha de dados.** 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Selecione a recolha de dados a partir das definições do menu de segurança.":::

## <a name="geolocation-and-ip-address-handling"></a>Geolocalização e tratamento de endereços IP 

Para garantir a sua solução IoT, os endereços IP das ligações de entrada e saída para os seus dispositivos IoT, IoT Edge e IoT Hub(s) são recolhidos e armazenados por padrão. Esta informação é essencial, e usada para detetar conectividade anormal a partir de fontes suspeitas de endereço IP. Por exemplo, quando há tentativas feitas que tentam estabelecer ligações a partir de uma fonte de endereço IP de uma botnet conhecida, ou de uma fonte de endereço IP fora da sua geolocalização. O serviço Defender for IoT, oferece a flexibilidade para permitir e desativar a recolha dos dados de endereço IP a qualquer momento. 

Para ativar ou desativar a recolha de dados de endereços IP: 

1. Abra o seu Hub IoT e, em seguida, selecione **Definições**   do menu **Segurança.**   

1. Selecione o ecrã **de Recolha de Dados**   e modifique as definições de geolocalização e tratamento de endereços IP de acordo com as suas necessidades. 

## <a name="log-analytics-creation"></a>Criação log analytics 

O Defender for IoT permite-lhe armazenar alertas de segurança, recomendações e dados de segurança brutos no seu espaço de trabalho Log Analytics. A ingestão de Log Analytics no IoT Hub está definida **por** defeito na solução Defender para IoT. É possível anexar o Defender for IoT a um espaço de trabalho Log Analytic e armazenar os dados de segurança lá também. 

Existem dois tipos de informação armazenados por padrão no seu espaço de trabalho Log Analytics pelo Defender para IoT:
 
- Alertas de segurança.

- Recomendações. 

Pode optar por adicionar armazenamento de um tipo de informação adicional como `raw events` . 

> [!Note] 
> O armazenamento  `raw events`   no Log Analytics tem custos adicionais de armazenamento. 

Para permitir que o Log Analytics funcione com o micro-agente: 

1. Navegue para **a configuração do espaço de trabalho** Recolha de  >  **dados** e altere o toggle para  **On**. 

1. Crie um novo espaço de trabalho Log Analytics ou anexe um existente. 

1. Verifique se a opção **de Acesso a dados de segurança bruta** está   selecionada.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Certifique-se de que o acesso aos dados de segurança brutos é selecionado.":::

1.  **Selecione Guardar**.

Todos os meses, os primeiros 5 gigabytes de dados ingeridos, por cliente, ao serviço Azure Log Analytics, são gratuitos. Cada gigabyte de dados ingeridos no seu espaço de trabalho Azure Log Analytics, é mantido gratuitamente durante os primeiros 31 dias. Para obter mais informações sobre preços, consulte, [registar preços](https://azure.microsoft.com/pricing/details/monitor/). 

Para alterar a configuração do espaço de trabalho do Log Analytics: 

1. No seu Hub IoT, no menu **Segurança,** selecione  **Definições**. 

1. Selecione o ecrã **de Recolha de Dados**   e modifique a configuração do espaço de trabalho das definições de Log Analytics de acordo com as suas necessidades. 

Para aceder aos seus alertas no seu espaço de trabalho Log Analytics após a configuração:

1. Selecione um alerta no Defender para IoT.

1. **Selecione Investigar alertas no espaço de trabalho Log Analytics**.

Para aceder aos seus alertas no seu espaço de trabalho Log Analytics após a configuração:

1. Selecione uma recomendação no Defender para IoT.

1. **Selecione Investigar recomendações no espaço de trabalho Log Analytics**. 
 
Para obter mais informações sobre a consulta de dados do Log Analytics, consulte [Começar com consultas no Log Analytics](../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Desligue o Defender para IoT 

Para ligar ou desligar um Defender para o serviço IoT, ou desligar-se num hub IoT específico: 

1. No seu Hub IoT, no menu **Segurança,** selecione  **Definições**.

1. Selecione o ecrã **de Recolha de Dados**   e modifique a configuração do espaço de trabalho das definições de Log Analytics de acordo com as suas necessidades.

## <a name="next-steps"></a>Passos seguintes 

Avance para o próximo artigo para configurar a [sua solução](quickstart-configure-your-solution.md).