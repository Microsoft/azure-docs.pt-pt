---
title: Ligue os dados da Atividade Azure ao Azure Sentinel [ Microsoft Docs
description: Saiba como ligar os dados da Atividade Azure ao Azure Sentinel.
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
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124991"
---
# <a name="connect-data-from-azure-activity-log"></a>Ligar dados do registo da Atividade Azure

Pode transmitir registos a partir do [Azure Activity](../azure-monitor/platform/platform-logs-overview.md) para o Azure Sentinel com um único clique. O registo de Atividades é um registo de subscrição que regista e exibe eventos de nível de subscrição em todo o Azure, desde dados operacionais do Gestor de Recursos do Azure até atualizações sobre eventos de Saúde de Serviço. Utilizando o registo de Atividades, pode determinar o "quê, quem e quando" para qualquer operação de escrita (PUT, POST, DELETE) realizada nos recursos da sua subscrição. Também pode conhecer o estado da operação e outras propriedades relevantes. O registo de atividadenão inclui operações ou operações de leitura (GET) para recursos que utilizem o modelo Classic/"RDFE". 

## <a name="prerequisites"></a>Pré-requisitos

- O utilizador deve ter permissões contributivas para o espaço de trabalho do Log Analytics.
- O utilizador deve ter permissões do Leitor para qualquer subscrição cujos registos pretenda transmitir para o Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Configurar o conector de atividade sinuosa

1. A partir do menu de navegação Azure Sentinel, selecione **conectores de dados**. A partir da lista de conectores, clique na **Atividade Azure,** e depois no botão de página do **conector Aberto** na parte inferior direita.

2. No separador **Instruções,** clique nos registos de configuração da **atividade do Azure >** link.

3. No painel de **log Da Atividade Azure,** selecione as subscrições cujos registos pretende transmitir para o Azure Sentinel. 

4. No painel de subscrição que se abre para a direita, clique em **Connect**.

5. Para utilizar o esquema relevante no Log Analytics para `AzureActivity` alertas de Atividade Azure, digite na janela de consulta.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o registo da Atividade Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar a detetar ameaças com o Azure Sentinel, usando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
