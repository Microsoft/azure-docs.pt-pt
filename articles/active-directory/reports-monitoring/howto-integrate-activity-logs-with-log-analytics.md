---
title: Stream Azure Ative Directory logy to Azure Monitor logs [ Diretórios Ativos de Streaming] Microsoft Docs
description: Saiba como integrar registos de Diretórios Ativos azure com registos do Monitor Azure
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 213fb6e73ae2fc4314320d0e3e593632d8eb7f85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266445"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrar registos de AD Azure com registos do Monitor Azure

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Os registos do Azure Monitor permitem consultar dados para encontrar eventos específicos, analisar tendências e realizar a correlação entre várias fontes de dados. Com a integração de registos de atividade da Azure AD nos registos do Monitor Azure, pode agora executar tarefas como:

 * Compare os seus registos de entrada em Anúncios Azure com os registos de segurança publicados pelo Azure Security Center

 * Problemas de desempenho na página de início de sessão da sua aplicação, correlacionar os dados de desempenho da aplicação a partir de Insights de Aplicação Azure.  

O vídeo seguinte de uma sessão ignite demonstra os benefícios de utilizar registos do Azure Monitor para registos de Anúncios Azure em cenários práticos de utilizador.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Neste artigo, aprende-se a integrar os registos de Diretórios Ativos azure (Azure AD) com o Azure Monitor.

## <a name="supported-reports"></a>Relatórios suportados

Pode encaminhar registos de atividade de auditoria e registos de atividade sessão para registos do Monitor Azure para análise mais aprofundada. 

* **Registos de auditoria**: o [relatório de atividades de registos de auditoria](concept-audit-logs.md) dá-lhe acesso ao histórico de cada tarefa que é executada no seu inquilino.
* **Registos de inícios de sessão**: com o [relatório de atividades de inícios de sessão](concept-sign-ins.md), pode saber quem executou as tarefas reportadas no relatório de registos de auditoria.

> [!NOTE]
> A auditoria relacionada com B2C e os registos de atividades de inícios de sessão não são suportados atualmente.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para utilizar esta funcionalidade, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [inscrever-se para obter uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um inquilino do Azure AD.
* Um utilizador que seja *administrador global* ou *administrador de segurança* do inquilino do Azure AD.
* Um espaço de trabalho log Analytics na sua subscrição Azure. Aprenda a criar um espaço de [trabalho log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Enviar registos para o Monitor Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Selecione > **Definições** -> de diagnóstico de **diretório ativo Azure****Adicione a definição de diagnóstico**. Também pode selecionar Definições de **Exportação** a partir da página **Registos** de Auditoria ou **Iniciars de Sessão** para aceder à página de configuração de configurações de diagnóstico.  
    
3. No menu de **definições** de diagnóstico, selecione a caixa de verificação do espaço de **trabalho Enviar para Registar Analytics** e, em seguida, selecione **Configurar**.

4. Selecione o espaço de trabalho do Log Analytics para o quais pretende enviar os registos ou crie um novo espaço de trabalho na caixa de diálogo fornecida.  

5. Escolha uma ou ambas as opções abaixo:
    * Para enviar registos de auditoria para o espaço de trabalho do Log Analytics, selecione a caixa de verificação **de Registos de Auditoria.** 
    * Para enviar registos de log-in para o espaço de trabalho do Log Analytics, selecione a caixa de verificação **SignInLogs.**

6. Selecione **Guardar** para guardar a definição.

    ![Definições de diagnósticos](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Após cerca de 15 minutos, verifique se os eventos são transmitidos para o seu espaço de trabalho Log Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Analise registos de atividade da Azure AD com registos do Monitor Azure](howto-analyze-activity-logs-log-analytics.md)
* [Instale e utilize as vistas de análise de registo para o Diretório Ativo Azure](howto-install-use-log-analytics-views.md)