---
title: Configure Azure Sentinel para Defender para IoT (pré-visualização)
description: Explica como configurar o Azure Sentinel para receber dados da sua solução Defender para ioT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940057"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Ligue os seus dados do Defender para IoT a Azure Sentinel (pré-visualização)

O Azure Security Center for IoT data connector em Azure Sentinel está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

Neste guia, saiba como ligar o seu Defender para dados IoT ao Azure Sentinel.

> [!div class="checklist"]
> * Pré-requisitos
> * Definições de ligação
> * Vista de alerta do Log Analytics

Ligue os alertas do Defender para IoT e transmita-os diretamente para o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter workspace **a ler** e **a escrever** permissões.
- **O Defender para IoT** deve ser **ativado** no seu(s) IoT Hub(s) relevante.
- Deve ter **lido** e **escrito** permissões no **Azure IoT Hub** que deseja ligar.
- Também deve ter **lido** e **escrito** permissões no **grupo de recursos Azure IoT Hub**.

> [!NOTE]
> Você deve ter o licenciamento de nível standard do Azure Security Center em execução na sua subscrição para enviar alertas de recursos gerais Azure. Com o licenciamento de nível livre exigido para o Defender para IoT, apenas o Defender para alertas relacionados com IoT será encaminhado para Azure Sentinel.

## <a name="connect-to-defender-for-iot"></a>Ligue-se ao Defender para IoT

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, clique no Defender para obter azulejos **IoT.**
1. A partir da parte inferior do painel direito, clique na **página do conector Aberto**.
1. Clique em **Connect**, ao lado de cada subscrição do IoT Hub cujos alertas e alertas de dispositivo pretende transmitir para Azure Sentinel.
    - Se o Defender for IoT não estiver ativado nesse Hub, verá uma mensagem de aviso ativa. Clique no link **Ativar** para iniciar e ativar o serviço.
1. Pode decidir se deseja que os alertas do Defender para ioT gerem automaticamente incidentes em Azure Sentinel. Em **InCosto incidentes**, selecione **Ativar** para permitir que a regra crie automaticamente incidentes a partir dos alertas gerados.  Esta regra pode ser alterada ou editada de acordo com as regras **Do Analytics**  >  **Ative.**

> [!NOTE]
>Pode levar 10 segundos ou mais para refrescar a lista de hubs depois de e fazer alterações de ligação.

## <a name="log-analytics-alert-display"></a>Visualização de alerta de Log Analytics

Para utilizar o esquema relevante no Log Analytics para visualizar os alertas de IoT do Defender:

1. Abrir **Registos**  >  **SecurityInsights**  >  **SecurityAlert,** ou procurar **SecurityAlert**.
1. Filtrar para ver apenas os alertas gerados pelo Defender para IoT utilizando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Notas de serviço

Depois de ligar um Hub IoT, os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Defender por IoT ao Azure Sentinel. Para saber mais sobre a deteção de ameaças e acesso a dados de segurança, consulte os seguintes artigos:

- Saiba como usar o Azure Sentinel para [obter visibilidade nos seus dados e ameaças potenciais.](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

- Saiba como aceder aos [seus dados de segurança IoT](how-to-security-data-access.md)
