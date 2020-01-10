---
title: Conectar dados de atividade do Azure ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar os dados de atividade do Azure ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 4c451c62a16a70d85d75ee00c3e08758e27425f6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749983"
---
# <a name="connect-data-from-azure-activity-log"></a>Conectar dados do log de atividades do Azure



Você pode transmitir logs do [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md) para o Azure Sentinel com um único clique. O log de atividades é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Tal inclui um intervalo de dados, desde dados operacionais do Azure Resource Manager a atualizações em eventos de Service Health. Usando o log de atividades, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) realizada nos recursos em sua assinatura. Também é possível compreender o estado da operação e outras propriedades relevantes. O log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo clássico/"RDFE". 


## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança


## <a name="connect-to-azure-activity-log"></a>Conectar-se ao log de atividades do Azure

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **log de atividades do Azure** .

2. No painel log de atividades do Azure, selecione as assinaturas que você deseja transmitir para o Azure Sentinel. 

3. Clique em **Ligar**.

4. Para usar o esquema relevante no Log Analytics para os alertas de atividade do Azure, procure **AzureActivity**.


 

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o log de atividades do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
