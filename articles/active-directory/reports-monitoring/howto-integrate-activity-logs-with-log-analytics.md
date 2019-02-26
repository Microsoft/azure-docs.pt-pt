---
title: Stream registos do Azure Active Directory para registos do Azure Monitor (pré-visualização) | Documentos da Microsoft
description: Saiba como integrar registos do Azure Active Directory com os registos do Azure Monitor (pré-visualização)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8259cdb61d2481805dd2e07c11b539e057215c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817070"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs-preview"></a>Integrar registos do Azure AD com os registos do Azure Monitor (pré-visualização)

Registos de Monitor do Azure permite-lhe dados de consulta para encontrar eventos específicos, analisar tendências e efetuar a correlação entre várias origens de dados. Com a integração do Azure AD registos de atividades nos registos do Azure Monitor, agora pode realizar tarefas como:

 * Compare os seus registos de início de sessão do AD do Azure em relação a registos de segurança publicados pelo centro de segurança do Azure

 * Resolver problemas de afunilamentos de desempenho na página de início de sessão da sua aplicação ao correlacionar dados de desempenho de aplicações do Azure Application Insights.  

O vídeo seguinte a partir de uma sessão Ignite demonstra as vantagens da utilização de registos do Azure Monitor para registos do Azure AD em cenários de usuário prático.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Neste artigo, saiba como integrar registos do Azure Active Directory (Azure AD) com o Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Relatórios suportados

Pode encaminhar os registos de atividades de auditoria e registos de atividades de início de sessão para registos do Azure Monitor para análise adicional. 

* **Registos de auditoria**: O [relatório de atividade de registos de auditoria](concept-audit-logs.md) permite-lhe aceder ao histórico de cada tarefa que é executada no seu inquilino.
* **Registos de início de sessão**: Com o [relatório de atividade de início de sessão](concept-sign-ins.md), pode determinar quem realizou as tarefas que são enviadas nos registos de auditoria.

> [!NOTE]
> A auditoria relacionada com B2C e os registos de atividades de inícios de sessão não são suportados atualmente.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para utilizar esta funcionalidade, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [inscrever-se para obter uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um inquilino do Azure AD.
* Um utilizador que seja *administrador global* ou *administrador de segurança* do inquilino do Azure AD.
* Uma área de trabalho do Log Analytics na sua subscrição do Azure. Saiba como [criar uma área de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor-logs"></a>Enviar registos para os registos do Azure Monitor

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione **do Azure Active Directory** > **definições de diagnóstico** -> **Adicionar definição de diagnóstico**. Também pode selecionar **Export Settings** partir do **registos de auditoria** ou **inícios de sessão** página para chegar à página de configuração das definições de diagnóstico.  
    
3. Na **das definições de diagnóstico** menu, selecione a **enviar para área de trabalho do Log Analytics** caixa de verificação e, em seguida, selecione **configurar**.

4. Selecione a área de trabalho do Log Analytics que pretende enviar os registos para ou crie uma nova área de trabalho na caixa de diálogo apresentada.  

5. Escolha uma ou ambas as opções abaixo:
    * Para enviar registos de auditoria para a área de trabalho do Log Analytics, selecione o **AuditLogs** caixa de verificação. 
    * Para enviar registos de início de sessão para a área de trabalho do Log Analytics, selecione o **SignInLogs** caixa de verificação.

6. Selecione **Guardar** para guardar a definição.

    ![Definições de diagnósticos](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Depois de cerca de 15 minutos, certifique-se de que os eventos são transmitidas em fluxo à sua área de trabalho do Log Analytics.

## <a name="next-steps"></a>Próximos Passos

* [Azure AD de analisar os registos de atividades com os registos do Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalar e utilizar os modos de exibição do log analytics do Azure Active Directory](howto-install-use-log-analytics-views.md)
