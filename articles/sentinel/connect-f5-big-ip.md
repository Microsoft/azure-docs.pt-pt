---
title: Ligue os dados do F5 BIG-IP ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar os dados DO F5 BIG-IP ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100092796"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Ligue o seu aparelho F5 BIG-IP 

O conector F5 BIG-IP permite-lhe conectar facilmente todos os seus registos F5 BIG-IP com Azure Sentinel, para visualizar livros de trabalho, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. A integração entre F5 BIG-IP e Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configure e ligue F5 BIG-IP 

F5 BIG-IP pode integrar e exportar registos diretamente para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione **F5 BIG-IP** e, em seguida, **abra a página do conector**. 
1. Para ligar o seu F5 BIG-IP, tem de publicar uma declaração JSON no ponto final da API do sistema. Para obter instruções sobre como fazê-lo, consulte [a Integração do F5 BIG-IP com o Azure Sentinel.](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)
8. A partir da página de conector F5 BIG-IP, copie o ID do Espaço de Trabalho e a Chave Primária e cole-os conforme instruído nos [dados de streaming para Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Depois de completar as instruções F5 BIG-IP, na página do conector Azure Sentinel, vê os tipos de dados ligados.
1. Para utilizar o esquema relevante no Log Analytics para os eventos F5 BIG-IP, procure **F5Telemetry_LTM_CL,** **F5Telemetry_system_CL** e **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar F5 BIG-IP ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


