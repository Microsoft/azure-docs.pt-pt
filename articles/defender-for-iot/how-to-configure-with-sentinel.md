---
title: Configure Azure Sentinel para Defender para IoT
description: Explica como configurar o Azure Sentinel para receber dados da sua solução Defender para ioT.
ms.topic: how-to
ms.date: 12/28/2020
ms.openlocfilehash: b481dd31b73e741d265a569076f1ddc076ad4a45
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778955"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>Ligue os seus dados do Defender para IoT a Azure Sentinel 

Utilize o conector Defender para IoT para transmitir todos os eventos do Defender para IoT em Azure Sentinel. 

Esta integração permite que as organizações detetem rapidamente ataques multi-fases que muitas vezes cruzam os limites de TI e OT. Além disso, a integração do Defender para a integração da IoT com as capacidades de orquestração, automação e resposta de Azure Sentinel (SOAR) permite uma resposta e prevenção automatizadas utilizando livros de reprodução otimizados em OT incorporados. 

## <a name="prerequisites"></a>Pré-requisitos

- **Leia** e **escreva** permissões no espaço de trabalho no qual a Azure Sentinel está implantada
- **O Defender for IoT** deve ser **ativado** no seu(s) IoT Hub(s) relevante
- Tem de ter permissões **do Contribuinte** na **Subscrição** que pretende ligar

## <a name="connect-to-defender-for-iot"></a>Ligue-se ao Defender para IoT

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, selecione o **Defender para IoT** (ainda pode ser chamado de Centro de Segurança Azure para IoT) da galeria.

1. A partir da parte inferior do painel direito, clique na **página do conector Aberto**.

1. Clique em **Connect**, ao lado de cada subscrição do IoT Hub cujos alertas e alertas de dispositivo pretende transmitir para Azure Sentinel.
    - Receberá uma mensagem de erro se o Defender for IoT não estiver ativado em pelo menos um Hub IoT dentro de uma subscrição. Ativar o Defender para IoT dentro do Hub IoT para remover o erro.

1. Pode decidir se deseja que os alertas do Defender para ioT gerem automaticamente incidentes em Azure Sentinel. Em **Configurar incidentes**, selecione **Ativar** para permitir que a regra de análise predefinida crie automaticamente incidentes a partir dos alertas gerados. Esta regra pode ser alterada ou editada de acordo com  >  **as regras** Do Analytics Ative .

> [!NOTE]
> Pode levar 10 segundos ou mais para a lista **de subscrições** atualizar depois de e fazer alterações de ligação. 

## <a name="log-analytics-alert-view"></a>Vista de alerta do Log Analytics

Para utilizar o esquema relevante no Log Analytics para visualizar os alertas de IoT do Defender:

1. Abrir **Registos**  >  **SecurityInsights**  >  **SecurityAlert,** ou procurar **SecurityAlert**.

1. Filtrar para ver apenas os alertas gerados pelo Defender para IoT utilizando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Notas de serviço

Após a ligação de uma **Subscrição,** os dados do hub estão disponíveis no Azure Sentinel aproximadamente 15 minutos depois.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Defender por IoT ao Azure Sentinel. Para saber mais sobre a deteção de ameaças e acesso a dados de segurança, consulte os seguintes artigos:

- Saiba como usar o Azure Sentinel para [obter visibilidade nos seus dados e ameaças potenciais.](../sentinel/quickstart-get-visibility.md)
- Saiba como aceder aos [seus dados de segurança IoT](how-to-security-data-access.md)