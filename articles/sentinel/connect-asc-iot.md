---
title: Ligue o Azure Defender ao IoT ao Azure Sentinel ! Microsoft Docs
description: Saiba como ligar o Azure Defender (anteriormente Azure Security Center) para obter dados IoT ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659613"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Ligue os seus dados do Azure Defender (anteriormente Azure Security Center) para IoT a Azure Sentinel 


> [!IMPORTANT]
> O Azure Defender para o conector de dados IoT está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utilize o Azure Defender para o conector IoT para transmitir todos os seus eventos Azure Defender para eventos IoT em Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- **Leia** e **escreva** permissões no espaço de trabalho no qual a Azure Sentinel está implantada
- **O Azure Defender for IoT** deve ser **ativado** no seu ioT Hub(s) relevante
- **Leia** e **escreva** permissões no **Azure IoT Hub** que pretende ligar
- **Leia** e **escreva** permissões no **grupo de recursos Azure IoT Hub**

## <a name="connect-to-azure-defender-for-iot"></a>Ligue-se ao Azure Defender para IoT

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, selecione **Azure Defender para IoT** (ainda pode ser chamado de Azure Security Center for IoT) da galeria.
1. A partir do painel inferior direito, clique na **página do conector Aberto**. 
1. Clique em **Connect**, ao lado de cada subscrição do IoT Hub cujos alertas e alertas de dispositivo pretende transmitir para Azure Sentinel. 
    - Se o Azure Defender for IoT não estiver ativado nesse Hub, verá uma mensagem de aviso **ativada.** Clique no link **Enable** para iniciar o serviço. 
1. Pode decidir se deseja que os alertas do Azure Defender para ioT gerem automaticamente incidentes em Azure Sentinel. In **Create incidents**, selecione **Ativar** para permitir que a regra analítica predefinida crie incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Esta regra pode ser alterada ou editada de acordo com as regras **Do Analytics**  >  **Ative.**

> [!NOTE]
> Pode levar algum tempo para que a lista do hub se refresque depois de fazer alterações de ligação. 

## <a name="log-analytics-alert-display"></a>Visualização de alerta de Log Analytics

Para utilizar o esquema relevante no Log Analytics para exibir o Azure Defender para alertas IoT:

1. Abrir **Registos**  >  **SecurityInsights**  >  **SecurityAlert,** ou procurar **SecurityAlert**. 
2. Filtrar para ver apenas alertas gerados por IoT do Azure Defender utilizando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de ligar um Hub IoT, os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.


## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Azure Defender para dados IoT ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
