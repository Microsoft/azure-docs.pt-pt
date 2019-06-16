---
title: Funis de informações da aplicação do Azure
description: Saiba como pode utilizar os funis para descobrir como os clientes estão a interagir com a sua aplicação.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 2cb7e15b701b53e74618c21bf219a355d495f985
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372934"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Descubra como os clientes estão a utilizar o seu aplicativo com o Application Insights funis

Compreender a experiência do cliente é de extrema importância para o seu negócio. Se seu aplicativo envolve várias fases, precisa saber se a maioria dos clientes estão a ser processada por todo o processo, ou se eles são terminando o processo em algum momento. A progressão por meio de uma série de etapas num aplicativo web é conhecida como um *funil*. Pode utilizar o Azure Application Insights funis para obter informações sobre os seus utilizadores e monitorizar as taxas de conversão passo a passo. 

## <a name="create-your-funnel"></a>Criar o seu funil
Antes de criar seu funil, decidir qual a pergunta que pretende responder. Por exemplo, pode querer saber o número de utilizadores está a ver a home page, exibindo um perfil do cliente e criar um pedido. Neste exemplo, os proprietários da empresa Fabrikam Fiber querem saber a percentagem de clientes que criar com êxito um pedido de cliente.

Aqui estão as etapas executadas para criar o seu funil.

1. Na ferramenta funis do Application Insights, selecione **New**.
1. Partir do **intervalo de tempo** menu pendente, selecione **últimos 90 dias**. Selecione **os meus funis** ou **partilhado funis**.
1. Partir do **passo 1** na lista pendente, selecione **índice**. 
1. Partir do **passo 2** lista, selecione **cliente**.
1. Partir do **passo 3** lista, selecione **criar**.
1. Adicionar um nome para o funil e selecione **guardar**.

Captura de ecrã seguinte mostra que um exemplo do tipo de dados, a ferramenta funis gera. Os proprietários de Fabrikam podem ver que durante os últimos 90 dias, por cento de 54.3 dos seus clientes que acederam a home page de criar um pedido de cliente. Também podem ver que 2,700 dos seus clientes veio para o índice na home page. Isto pode indicar um problema de atualização.


![Ferramenta de captura de ecrã de funis com dados](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funis funcionalidades
Captura de ecrã anterior inclui cinco áreas destacadas. Esses são recursos de funis. A lista a seguir explica mais sobre cada área correspondente na captura de ecrã:
1. Se a sua aplicação é o objeto de amostragem, verá uma faixa de amostragem. Selecionar a faixa é aberto um painel de contexto, que explica como desativar a amostragem. 
2. Pode exportar o seu funil para [Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Selecione um passo para ver mais detalhes à direita. 
4. O gráfico de conversão do histórico mostra as taxas de conversão durante os últimos 90 dias. 
5. Compreenda melhor os seus utilizadores ao aceder a ferramenta utilizadores. Pode utilizar filtros em cada etapa. 

## <a name="next-steps"></a>Passos Seguintes
  * [Descrição geral da utilização](usage-overview.md)
  * [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
  * [Retenção](usage-retention.md)
  * [Livros](../../azure-monitor/app/usage-workbooks.md)
  * [Adicionar contexto de utilizador](usage-send-user-context.md)
  * [Exportar para o Power BI](../../azure-monitor/app/export-power-bi.md )

