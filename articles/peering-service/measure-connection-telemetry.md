---
title: 'Serviço de Peering Azure: Como medir a telemetria de conexão '
description: Neste tutorial aprenda a medir a telemetria de conexão.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022501"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Tutorial: Medição de telemetria de conexão de serviço de observação

 Neste tutorial, você aprenderá a medir a telemetria para as suas ligações de Serviço de Observação.
 
 A telemetria de conexão fornece insights recolhidos para a conectividade entre a localização do cliente e a rede Microsoft. Neste artigo, você vai aprender a ver o relatório de latência para uma ligação específica do Serviço de Peering Azure. 

Para medir a telemetria de conexão de serviço de peering, deve registar uma ligação de Serviço de Peering no portal Azure. Para saber como registar uma ligação, consulte [registar uma ligação de Serviço de Pares - portal Azure](azure-portal.md).


## <a name="view-a-latency-report"></a>Ver um relatório de latência

Para visualizar um relatório de latência para uma ligação específica do Serviço de Peering, siga estes passos.

1. Selecione **Todos os recursos** no painel esquerdo e selecione a ligação serviço de peering. Em seguida, selecione **Abrir** em **Prefixos**. 

   ![Selecione a ligação serviço de peering](./media/peering-service-measure/peering-service-measure-menu.png)

2. Aparece uma página de relatório de latência para todos os prefixos associados a essa ligação de Serviço de Peering. 

      ![Página de relatório de latência](./media/peering-service-measure/peering-service-latency-report.png)

3. Por predefinição, o relatório é atualizado para cada hora que é exibida nesta página. Para visualizar o relatório para diferentes linhas de tempo, escolha a opção adequada a partir dos **dados** do Show para o último . 

4. Para visualizar eventos para um prefixo específico, selecione o nome do prefixo e selecione **Prefix Events** no painel esquerdo. Os eventos capturados são exibidos.


   ![Eventos prefixos](./media/peering-service-measure/peering-service-prefix-event.png)

 Alguns dos possíveis eventos que são capturados na lista **de Eventos prefixos** são mostrados aqui.

| **Eventos pré-fixos** | **Tipo de evento**|**Raciocínio**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Informações|Anúncio de prefixo foi recebido|
| PrefixWithdrawalevent|Aviso| A retirada do prefixo foi recebida |
| PrefixBackupRouteAnnouncementEvent |Informações|Anúncio de rota de backup prefixo foi recebido |
| PrefixBackupRouteWithdrawalevent|Aviso|A retirada da rota de backup prefixo foi recebida |
| PrefixActivePath |Informações| Rota ativa de prefixo atual   |
| PrefixBackupPath | Informações|Rota de backup pré-prefixo atual   |
| PrefixOriginAsChangeEvent|Crítico| Prefixo exato recebido com número de sistema autónomo de origem diferente (para rota ativa)| 
| PrefixBackupRouteOriginAsChangeEEvent  | Erro|Prefixo recebido com número de sistema autónomo de origem diferente (para rota de backup)  |

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação do Serviço de Peering, consulte [a ligação do Serviço de Peering](connection.md).
- Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).