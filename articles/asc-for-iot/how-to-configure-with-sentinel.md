---
title: Guia azure security center for IoT para configuração com Azure Sentinel (pré-visualização) Microsoft Docs
description: Explica como configurar o Azure Sentinel para receber dados do seu Centro de Segurança Azure para a solução IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 082b33332051fee9da2aebe63b0c41edb300afaf
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303550"
---
> [!IMPORTANT]
> O Centro de Segurança Azure para o conector de dados IoT em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Ligue os seus dados do Azure Security Center para IoT ao Azure Sentinel (pré-visualização) 

Neste guia, aprenda a ligar o seu Centro de Segurança Azure para dados IoT ao Azure Sentinel.  

> [!div class="checklist"]
> * Pré-requisitos 
> * Definições de ligação
> * Vista de alerta de Log Analytics 

Ligue os alertas do Azure Security Center para ioT e transmita-os diretamente para O Sentinel a D.C.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter espaço de trabalho **para ler** e **escrever** permissões.
- O **Azure Security Center for IoT** deve estar **ativado** no seu(s Hub's IoT relevante).
- Você deve ter **lido** e **escrito** permissões no **Hub Azure IoT** que você deseja ligar.
- Também deve ter **lido** e **escrito** permissões no grupo de **recursos Azure IoT Hub**.

> [!NOTE]
> Deve ter o licenciamento de nível Azure Security Center Standard a funcionar na sua subscrição para enviar alertas gerais de recursos Azure. Com o licenciamento de nível livre necessário para o Azure Security Center for IoT, apenas o Azure Security Center para alertas relacionados com IoT será encaminhado para o Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Ligue-se ao Centro de Segurança Azure para IoT

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no Centro de **Segurança Azure para azulejos IoT.**
1. A partir da parte inferior do painel direito, clique na **página do conector Aberto**. 
1. Clique em **Connect**, ao lado de cada subscrição do IoT Hub cujos alertas e alertas de dispositivo deseja transmitir para o Azure Sentinel. 
    - Se o Azure Security Center for IoT não estiver ativado no Hub, verá uma mensagem de aviso Enable. Clique no link **'Ativar'** para iniciar e ativar o serviço. 
1. Pode decidir se deseja que os alertas do Azure Security Center para ioT gerem automaticamente incidentes em Azure Sentinel. Em caso **de criar incidentes,** selecione **Enable** para permitir que a regra crie automaticamente incidentes a partir dos alertas gerados.  Esta regra pode ser alterada ou editada de acordo com as regras **do Analytics** > **Ative.**

> [!NOTE]
>Pode levar 10 segundos ou mais para refrescar a lista do hub depois de fazer alterações de ligação. 

## <a name="log-analytics-alert-display"></a>Ecrã de alerta De Log Analytics

Para utilizar o esquema relevante no Log Analytics para exibir o Centro de Segurança Azure para alertas IoT:

1. Abrir **registos** > **SecurityInsights** > **SecurityAlert**, ou procurar **SecurityAlert**. 
2. Filtrar para ver apenas o Azure Security Center for IoT gerou alertas utilizando o filtro kql seguinte:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de ligar um Hub IoT, os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.


## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Azure Security Center for IoT ao Azure Sentinel. Para saber mais sobre deteção de ameaças e acesso a dados de segurança, consulte os seguintes artigos:

- Aprenda a usar o Azure Sentinel para [obter visibilidade nos seus dados e ameaças potenciais.](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

- Saiba como aceder aos seus dados de [segurança IoT](how-to-security-data-access.md)