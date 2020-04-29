---
title: Ligue os dados f5 BIG-IP ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados F5 BIG-IP ao Azure Sentinel.
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
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588285"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Ligue o seu aparelho F5 BIG-IP 

> [!IMPORTANT]
> O conector de dados F5 BIG-IP em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O conector F5 BIG-IP permite-lhe ligar facilmente todos os seus registos F5 BIG-IP com o Azure Sentinel, ver livros de trabalho, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. A integração entre f5 BIG-IP e Azure Sentinel faz uso da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configure e ligue F5 BIG-IP 

F5 BIG-IP pode integrar e exportar registos diretamente para O Sentinela.

1. No portal Azure Sentinel, clique em **conectores de dados** e selecione **F5 BIG-IP** e, em seguida, abra a **página do conector**. 
1. Para ligar o f5 BIG-IP, tem de publicar uma declaração jSON no ponto final da API do sistema. Para obter instruções sobre como fazê-lo, consulte [Integração do F5 BIG-IP com O Sentinel Apinávia](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. A partir da página do conector F5 BIG-IP, copie o ID do Espaço de Trabalho e a Chave Primária e cole-os conforme instruído em dados de [streaming para O Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Depois de completar as instruções F5 BIG-IP, na página do conector Azure Sentinel, consulte os tipos de dados ligados.
1. Para utilizar o esquema relevante no Log Analytics para os eventos F5 BIG-IP, procure **F5Telemetry_LTM_CL,** **F5Telemetry_system_CL**e **F5Telemetry_ASM_CL.**


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar f5 BIG-IP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


