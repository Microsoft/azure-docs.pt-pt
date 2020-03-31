---
title: Ligue o Centro de Segurança Azure para IoT a Azure Sentinel [ Microsoft Docs
description: Saiba como ligar o Azure Security Center para dados IoT ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588642"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Ligue os seus dados do Azure Security Center para IoT ao Azure Sentinel 


> [!IMPORTANT]
> O Centro de Segurança Azure para o conector de dados IoT encontra-se atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Utilize o Centro de Segurança Azure para conector IoT para transmitir todo o seu Centro de Segurança Azure para eventos IoT em Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- **Ler** e **escrever** permissões no Espaço de Trabalho no qual o Azure Sentinel está implantado
- O Centro de **Segurança Azure para IoT** deve ser **ativado** no seu(s) Hub(s) IoT relevante
- **Leia** e **escreva** permissões no **Hub Azure IoT** que pretende ligar
- **Ler** e **escrever** permissões no grupo de **recursos Azure IoT Hub**

> [!NOTE]
> Embora tenha de ativar a licença de nível **Standard** do Azure Security Center na sua subscrição para transmitir alertas de recursos IoT para o Azure Sentinel, basta permitir a licença de nível **Free** do Azure Security Center na sua subscrição para visualizar o Azure Security Center para alertas IoT em Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Ligue-se ao Centro de Segurança Azure para IoT

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no Centro de **Segurança Azure para azulejos IoT.**
1. A partir do painel inferior direito, clique na **página do conector Aberto**. 
1. Clique em **Connect**, ao lado de cada subscrição do IoT Hub cujos alertas e alertas de dispositivo deseja transmitir para o Azure Sentinel. 
    - Se o Azure Security Center for IoT não estiver ativado nesse Hub, verá uma mensagem de aviso **Enable.** Clique no link **'Ativar'** para iniciar o serviço. 
1. Pode decidir se deseja que os alertas do Azure Security Center para ioT gerem automaticamente incidentes em Azure Sentinel. Em caso **de criar incidentes,** selecione **Enable** para ativar a regra analítica padrão para criar incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Esta regra pode ser alterada ou editada de acordo com as regras **do Analytics** > **Ative.**

> [!NOTE]
> Pode levar algum tempo para a lista de hub refrescar depois de fazer alterações de ligação. 

## <a name="log-analytics-alert-display"></a>Ecrã de alerta De Log Analytics

Para utilizar o esquema relevante no Log Analytics para exibir o Centro de Segurança Azure para alertas IoT:

1. Abrir **Registos** > **SecurityInsights** > **SecurityAlert,** ou procurar **SecurityAlert**. 
2. Filtrar para ver apenas o Azure Security Center for IoT gerou alertas utilizando o filtro kql seguinte:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de ligar um Hub IoT, os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.


## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Azure Security Center para dados IoT ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.
