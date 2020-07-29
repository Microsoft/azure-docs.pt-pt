---
title: Ligue o Centro de Segurança Azure para IoT a Azure Sentinel Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588642"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Ligue os seus dados do Azure Security Center para IoT a Azure Sentinel 


> [!IMPORTANT]
> O Centro de Segurança Azure para o conector de dados IoT está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utilize o Centro de Segurança Azure para o conector IoT para transmitir todo o seu Centro de Segurança Azure para eventos IoT em Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- **Leia** e **escreva** permissões no espaço de trabalho no qual a Azure Sentinel está implantada
- **O Centro de Segurança Azure para IoT** deve ser **ativado** no seu(s) IoT Hub(s) relevante
- **Leia** e **escreva** permissões no **Azure IoT Hub** que pretende ligar
- **Leia** e **escreva** permissões no **grupo de recursos Azure IoT Hub**

> [!NOTE]
> Embora tenha de ativar a licença de nível **Standard** do Azure Security Center na sua subscrição para transmitir alertas de recursos IoT para Azure Sentinel, basta ativar a licença de nível **gratuito** do Azure Security Center na sua subscrição para visualizar o Azure Security Center para alertas IoT em Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Ligue-se ao Centro de Segurança Azure para IoT

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, clique no Centro de Segurança Azure para obter azulejos **IoT.**
1. A partir do painel inferior direito, clique na **página do conector Aberto**. 
1. Clique em **Connect**, ao lado de cada subscrição do IoT Hub cujos alertas e alertas de dispositivo pretende transmitir para Azure Sentinel. 
    - Se o Centro de Segurança Azure para IoT não estiver ativado nesse Hub, verá uma mensagem de aviso **ativada.** Clique no link **Enable** para iniciar o serviço. 
1. Pode decidir se quer que os alertas do Azure Security Center para ioT gerem automaticamente incidentes em Azure Sentinel. In **Create incidents**, selecione **Ativar** para permitir que a regra analítica predefinida crie incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Esta regra pode ser alterada ou editada de acordo com as regras **Do Analytics**  >  **Ative.**

> [!NOTE]
> Pode levar algum tempo para que a lista do hub se refresque depois de fazer alterações de ligação. 

## <a name="log-analytics-alert-display"></a>Visualização de alerta de Log Analytics

Para utilizar o esquema relevante no Log Analytics para exibir o Centro de Segurança Azure para alertas IoT:

1. Abrir **Registos**  >  **SecurityInsights**  >  **SecurityAlert,** ou procurar **SecurityAlert**. 
2. Filtrar para ver apenas o Centro de Segurança Azure para alertas gerados por IoT utilizando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de ligar um Hub IoT, os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.


## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar o Azure Security Center para dados IoT ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
