---
title: Ligue os dados da Squadra Technologies secRMM ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados secRMM da Squadra Technologies ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588115"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Conecte os dados secRMM da Squadra Technologies ao Azure Sentinel 

> [!IMPORTANT]
> O conector de dados Squadra Technologies Security Remoble Media Manager (secRMM) em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .


O conector SecRMM da Squadra Technologies permite-lhe ligar facilmente os seus registos de solução de segurança Squadra Technologies secRMM com o Azure Sentinel. Permite-lhe ver dashboards, criar alertas personalizados e melhorar a investigação. Este conector dá-lhe informações sobre eventos de armazenamento amovíveis USB. A integração entre a Squadra Technologies secRMM e a Azure Sentinel utiliza a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configure e ligue o secRMM das Tecnologias Squadra 

A Squadra Technologies secRMM pode integrar e exportar registos diretamente para o Azure Sentinel.
1. No portal Azure Sentinel, clique em conectores de dados e selecione Squadra Technologies secRMM e, em seguida, abra a página do conector.

2. Siga os passos descritos no guia de embarque da [Squadra Technologies para o Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) obter dados squadra secRMM em Azure Sentinel.   


## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do CustomLogs secRMM_CL.
Para utilizar o esquema relevante no Log Analytics para o secRMM das Tecnologias Squadra, procure secRMM_CL.

## <a name="validate-connectivity"></a>Validar a conectividade
Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a Squadra Technologies secRMM ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

