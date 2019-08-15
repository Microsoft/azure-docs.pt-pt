---
title: Transmitir logs de Azure Active Directory para Azure Monitor logs | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com logs de Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: bffe16d604ac6b86b489092f50fbdc0b856867b3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989775"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrar logs do Azure AD com logs de Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Os logs de Azure Monitor permitem que você consulte dados para localizar eventos específicos, analisar tendências e executar a correlação entre várias fontes de dados. Com a integração dos logs de atividades do Azure AD nos logs de Azure Monitor, agora você pode executar tarefas como:

 * Compare seus logs de entrada do Azure AD com os logs de segurança publicados pela central de segurança do Azure

 * Solucione problemas de afunilamentos de desempenho na página de entrada do seu aplicativo correlacionando dados de desempenho do aplicativo de insights Aplicativo Azure.  

O vídeo a seguir de uma sessão Ignite demonstra os benefícios de usar os logs de Azure Monitor para logs do Azure AD em cenários de usuário práticos.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Neste artigo, você aprenderá a integrar os logs do Azure Active Directory (Azure AD) ao Azure Monitor.

## <a name="supported-reports"></a>Relatórios suportados

Você pode rotear logs de atividade de auditoria e logs de atividade de entrada para Azure Monitor logs para análise adicional. 

* **Logs de auditoria**: O [relatório de atividade de logs de auditoria](concept-audit-logs.md) fornece acesso ao histórico de todas as tarefas que são executadas em seu locatário.
* **Logs de entrada**: Com o [relatório de atividade de entrada](concept-sign-ins.md), você pode determinar quem realizou as tarefas que são relatadas nos logs de auditoria.

> [!NOTE]
> A auditoria relacionada com B2C e os registos de atividades de inícios de sessão não são suportados atualmente.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para utilizar esta funcionalidade, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [inscrever-se para obter uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um inquilino do Azure AD.
* Um utilizador que seja *administrador global* ou *administrador de segurança* do inquilino do Azure AD.
* Um espaço de trabalho Log Analytics em sua assinatura do Azure. Saiba como [criar um espaço de trabalho log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Enviar logs para Azure Monitor

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione **Azure Active Directory** > configuraçõesde -> diagnóstico**Adicionar configuração de diagnóstico**. Você também pode selecionar **exportar configurações** na página **logs de auditoria** ou **entradas** para acessar a página de configuração configurações de diagnóstico.  
    
3. No menu **configurações de diagnóstico** , marque a caixa de seleção **Enviar para log Analytics espaço de trabalho** e, em seguida, selecione **Configurar**.

4. Selecione o Log Analytics espaço de trabalho para o qual você deseja enviar os logs ou crie um novo espaço de trabalho na caixa de diálogo fornecida.  

5. Escolha uma ou ambas as opções abaixo:
    * Para enviar logs de auditoria para o espaço de trabalho Log Analytics, marque a caixa de seleção **AuditLogs** . 
    * Para enviar logs de entrada para o espaço de trabalho Log Analytics, marque a caixa de seleção **SignInLogs** .

6. Selecione **Guardar** para guardar a definição.

    ![Definições de diagnóstico](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Após cerca de 15 minutos, verifique se os eventos são transmitidos para seu espaço de trabalho Log Analytics.

## <a name="next-steps"></a>Passos Seguintes

* [Analisar logs de atividades do Azure AD com logs de Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalar e usar as exibições do log Analytics para Azure Active Directory](howto-install-use-log-analytics-views.md)