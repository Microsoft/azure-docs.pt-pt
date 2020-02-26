---
title: Ligue os dados da Atividade Azure ao Azure Sentinel  Microsoft Docs
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 28d5acd80deef193c7d2fea46f682f26abd756aa
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588608"
---
# <a name="connect-data-from-azure-activity-log"></a>Ligar dados do registo da Atividade Azure



Pode transmitir registos a partir do [Azure Activity](../azure-monitor/platform/platform-logs-overview.md) para o Azure Sentinel com um único clique. O registo de atividadeé um registo de subscrição que fornece informações sobre eventos de nível de subscrição que ocorreram no Azure. Isto inclui uma gama de dados, desde dados operacionais do Gestor de Recursos do Azure até atualizações sobre eventos de Saúde de Serviço. Utilizando o registo de Atividades, pode determinar o "quê, quem e quando" para qualquer operação de escrita (PUT, POST, DELETE) assumido sondar os recursos da sua subscrição. Também é possível compreender o estado da operação e outras propriedades relevantes. O registo de atividadenão inclui operações ou operações de leitura (GET) para recursos que utilizem o modelo Classic/"RDFE". 


## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões contributivas para log Analytics espaço de trabalho 


## <a name="connect-to-azure-activity-log"></a>Ligue-se ao registo de atividades do Azure

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no azulejo de **log da Atividade Azure.**

2. No painel de log Da Atividade Azure, selecione as subscrições que pretende transmitir para o Azure Sentinel. 

3. Clique em **Ligar**.

4. Para utilizar o esquema relevante no Log Analytics para os alertas de Atividade Azure, procure o **AzureActivity**.


 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o registo da Atividade Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
