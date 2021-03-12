---
title: Integre o Splunk utilizando o monitor Azure | Microsoft Docs
description: Saiba como integrar os registos do Azure Ative Directory com o Splunk utilizando o Azure Monitor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6a597d4fd58646f56e271cb6027fb46db1e26
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634231"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Como: Integrar registos de diretório ativo do Azure com splunk usando o Azure Monitor

Neste artigo, aprende-se a integrar os registos do Azure Ative Directory (Azure AD) com o Splunk utilizando o Azure Monitor. Primeiro, encaminha os registos para um centro de eventos Azure, e depois integra o centro de eventos com o Splunk.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:

- Um centro de eventos Azure que contém registos de atividade azure AD. Saiba como [transmitir os seus registos de atividade para um centro de eventos.](./tutorial-azure-monitor-stream-logs-to-event-hub.md) 

-  O [Add-on Splunk para os Serviços microsoft cloud](https://splunkbase.splunk.com/app/3110/#/details). 

## <a name="integrate-azure-active-directory-logs"></a>Integre registos de diretório ativo Azure 

1. Abra a sua instância Splunk e selecione **Resumo de Dados**.

    ![O botão "Resumo de Dados"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecione o **separador Tipos de Fontes** e, em seguida, selecione **amal: aadal:audit**

    ![O separador de tipos de fontes de resumo de dados](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Os registos de atividade AZure AD são apresentados na seguinte figura:

    ![Registos de atividade](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se não conseguir instalar um addon no seu exemplo Splunk (por exemplo, se estiver a utilizar um proxy ou a correr em Splunk Cloud), pode encaminhar estes eventos para o Splunk HTTP Event Collector. Para tal, utilize esta [função Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é desencadeada por novas mensagens no centro de eventos. 
>

## <a name="next-steps"></a>Passos seguintes

* [Interpretar esquema de registos de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)