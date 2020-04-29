---
title: Integrar o Splunk utilizando o Monitor Azure [ Monitor Azul] Microsoft Docs
description: Saiba como integrar registos de Diretórios Ativos Azure com a SumoLogic utilizando o Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968700"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Como: Integrar registos de Diretórios Ativos Azure com Splunk utilizando o Monitor Azure

Neste artigo, aprende-se a integrar os registos de Logigem Ativa azure (Azure AD) com a Splunk utilizando o Azure Monitor. Você primeiro encaminha os registos para um centro de eventos Azure, e depois integra o centro de eventos com splunk.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:

- Um centro de eventos Azure que contém registos de atividade da Azure AD. Aprenda a transmitir os seus registos de [atividade para um centro de eventos.](quickstart-azure-monitor-stream-logs-to-event-hub.md) 

-  O [Microsoft Azure Add on for Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Integrar registos de Diretório Ativo Azure 

1. Abra a sua instância Splunk e selecione **Data Sumário**.

    ![O botão "Resumo de Dados"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecione o separador **Sourcetypes** e, em seguida, selecione **amal: aadal:audit**

    ![O separador Fontes Sumárias de Dados](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Os registos de atividade da AD Azure são apresentados na seguinte figura:

    ![Registos de atividade](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se não conseguir instalar um addon na sua instância Splunk (por exemplo, se estiver a utilizar um proxy ou a correr na Nuvem Splunk), pode encaminhar estes eventos para o Splunk HTTP Event Collector. Para tal, utilize esta [função Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é desencadeada por novas mensagens no centro do evento. 
>

## <a name="next-steps"></a>Passos seguintes

* [Interpretar esquemas de registos de auditoria no Monitor Azure](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)