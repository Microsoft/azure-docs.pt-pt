---
title: Recolher dados de atividade do Azure em pré-visualização sentinela do Azure | Documentos da Microsoft
description: Saiba como recolher dados de atividade do Azure no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 068da0c196cba8095aca24a572eb4dfaec685c7a
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992944"
---
# <a name="collect-data-from-azure-activity-log"></a>Recolher dados de registo de atividades do Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo registos a partir [registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md) em Azure sentinela com um único clique. O registo de atividades é um registo de subscrição que fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. Isto inclui uma variedade de dados de dados operacionais do Azure Resource Manager para as atualizações em eventos de estado de funcionamento do serviço. Utilizar o registo de atividades, pode determinar o "o quê, quem e quando" para qualquer escrever operação (PUT, POST, DELETE) efetuada nos recursos na sua subscrição. Também é possível compreender o estado da operação e outras propriedades relevantes. O registo de Atividades não incluem operações de leitura (GET) nem operações para recursos que utilizar clássica / modelo de "RDFE". 


## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões de administrador de segurança ou de administrador global


## <a name="connect-to-azure-activity-log"></a>Ligar ao registo de atividades do Azure

1. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **registo de atividades do Azure** mosaico.

2. No painel de registo de atividade do Azure, selecione as subscrições que pretende transmitir para o Azure sentinela. 

3. Clique em **Ligar**.

4. Para utilizar o esquema relevante no Log Analytics para os alertas de atividade do Azure, procure **AzureActivity**.


 

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar o registo de atividades do Azure ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
