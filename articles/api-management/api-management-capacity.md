---
title: Capacidade de uma instância de gestão de API do Azure | Documentos da Microsoft
description: Este artigo explica o que é a métrica de capacidade e como tomar decisões informadas se pretende dimensionar uma instância de gestão de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454512"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacidade de uma instância de gestão de API do Azure

**Capacidade** é a única, mais importante [métrica do Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) para tomar decisões informadas se pretende dimensionar uma instância de gestão de API para acomodar mais carga. Sua construção é complexa e impõe determinado comportamento.

Este artigo explica o que o **capacidade** é e como ele se comporta. Ele mostra como acessar **capacidade** métricas no portal do Azure e sugere quando considerar o dimensionamento ou a atualizar a instância de gestão de API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Qual é a capacidade

![Métrica de capacidade](./media/api-management-capacity/capacity-ingredients.png)

**Capacidade** é um indicador de carga numa instância APIM. Ela reflete a utilização de recursos (CPU, memória) e comprimentos de fila de rede. Utilização da CPU e memória revela o consumo de recursos por:

+ Serviços APIM, como ações ou pedido de processamento de gestão, que pode incluir pedidos de reencaminhamento ou executar uma política
+ processos de sistema operativo selecionado, incluindo processos que envolvem o custo de handshakes de SSL em novas ligações.

Total **capacidade** é uma média de seus próprios valores de cada unidade de uma instância de gestão de API.

## <a name="capacity-metric-behavior"></a>Comportamento da métrica capacidade

Por causa de sua construção, na vida real **capacidade** pode ser afetado por muitas variáveis, por exemplo:

+ padrões de ligação (nova ligação numa vs pedido reutilizar a ligação existente)
+ tamanho de um pedido e resposta
+ políticas configuradas em cada API ou o número de clientes enviando solicitações.

As operações mais complexas nas solicitações são, mais alto a **capacidade** consumo será. Por exemplo, políticas de transformação complexas consumam CPU muito mais que um reencaminhamento de solicitação simples. As respostas de serviço de back-end lenta serão aumentá-la demasiado.

> [!IMPORTANT]
> **Capacidade** não é uma medida direta do número de pedidos a serem processados.

![Picos de métricos de capacidade](./media/api-management-capacity/capacity-spikes.png)

**Capacidade** também pode expandam intermitentemente ou ser maior que zero, mesmo se não houver nenhuma solicitação a ser processada. Ele acontece devido a ações específicas do sistema ou da plataforma e não deve ser levado em consideração ao decidir se uma instância de dimensionar.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Utilizar o Portal do Azure para examinar a capacidade
  
![Métrica de capacidade](./media/api-management-capacity/capacity-metric.png)  

1. Navegue para a instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **métricas (pré-visualização)**.
3. Na secção de roxa, selecione **capacidade** métrica de métricas disponíveis e deixe a predefinição **média** agregação.

    > [!TIP]
    > Sempre deve examinar um **capacidade** métrica divisão por localização para evitar interpretações erradas.

4. Na secção de verde, selecione **localização** para dividir a métrica por dimensão.
5. Escolha um período de tempo pretendido da barra superior da seção.

    Pode definir um alerta de métrica para informá-lo quando algo inesperado está a acontecer. Por exemplo, obter notificações quando a instância APIM tem sido a exceder a sua capacidade máxima esperada durante mais de 20 minutos.

    >[!TIP]
    > Pode configurar alertas para permitem-lhe saber quando seu serviço está a esgotar na capacidade ou utilizar a funcionalidade de dimensionamento automático do Azure Monitor para adicionar automaticamente uma unidade de API Management do Azure. Operação de dimensionamento, pode demorar cerca de 30 minutos, para que deve planejar suas regras em conformidade.  
    > Dimensionamento apenas a localização principal é permitido.

## <a name="use-capacity-for-scaling-decisions"></a>Utilizar a capacidade para dimensionar decisões

**Capacidade** é a métrica de tomada de decisões se pretende dimensionar uma instância de gestão de API para acomodar mais carga. Considere:

+ Observar uma tendência de longo prazo e a média.
+ A ignorar a picos repentinos, que são mais prováveis que não relacionadas com qualquer aumento de carga (consulte a seção de "Comportamento de métricas de capacidade" para explicação).
+ Atualizar ou dimensionar a sua instância, quando **capacidade**do valor excede 60% ou 70% durante um período mais longo do tempo (por exemplo, 30 minutos). Valores diferentes podem funcionar melhor para seu cenário ou serviço.

>[!TIP]  
> Se for capaz de estimar o tráfego com antecedência, teste sua instância APIM nas cargas de trabalho esperado. Pode aumentar gradualmente a carga de pedidos no seu inquilino e monitorizar a qual o valor da métrica capacidade corresponde ao seu pico de carga. Siga os passos da secção anterior para utilizar o portal do Azure para compreender a capacidade é utilizada em qualquer momento.

## <a name="next-steps"></a>Passos Seguintes

[Como dimensionar ou atualizar uma instância de serviço de gestão de API do Azure](upgrade-and-scale.md)