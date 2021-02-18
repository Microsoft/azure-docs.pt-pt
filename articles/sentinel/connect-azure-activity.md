---
title: Ligue os dados da Atividade Azure ao Azure Sentinel | Microsoft Docs
description: Stream Azure Activity entra em Azure Sentinel com um único clique. O Registo de Atividade regista e exibe eventos de nível de subscrição em todo o Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595455"
---
# <a name="connect-data-from-azure-activity-log"></a>Conecte dados do registo de atividades Azure

Pode transmitir registos do [Azure Activity para](../azure-monitor/essentials/platform-logs-overview.md) O Azure Sentinel com um único clique. O registo de atividades é um registo de subscrição que regista e exibe eventos de nível de subscrição em todo o Azure, desde dados operacionais do Azure Resource Manager até atualizações sobre eventos de Saúde de Serviço. Utilizando o registo de Atividade, pode determinar o 'o quê, quem e quando' para qualquer operação de escrita (PUT, POST, DELETE) realizada nos recursos da sua subscrição. Também pode conhecer o estado da operação e outras propriedades relevantes. O registo de Atividades não inclui operações de leitura (GET) ou operações para recursos que utilizem o modelo Classic/"RDFE". 

## <a name="prerequisites"></a>Pré-requisitos

- O seu utilizador deve ter permissões do Contribuinte para o espaço de trabalho Do Log Analytics.
- O seu utilizador deve ter permissões do Reader para qualquer subscrição cujos registos pretende transmitir para o Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Configurar o conector de atividade azure

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**. A partir da lista de conectores, clique na **Atividade Azure** e, em seguida, no botão de página do **conector Open** no lado inferior direito.

2. No separador **Instruções,** clique nos **registos de Atividade Configure >** link.

3. No painel de registo de **atividades Azure,** selecione as subscrições cujos registos pretende transmitir para Azure Sentinel. 

4. No painel de subscrição que se abre para a direita, clique em **Connect**.

5. Para utilizar o esquema relevante no Log Analytics para alertas de Atividade Azure, digite `AzureActivity` na janela de consulta.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o log da Azure Activity ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
