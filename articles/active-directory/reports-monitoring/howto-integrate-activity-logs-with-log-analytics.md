---
title: Stream Azure Ative Directy regista registos para registos do Monitor Azure Microsoft Docs
description: Saiba como integrar os registos do Azure Ative Directory com registos do Azure Monitor
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f762ef1fa3a997c01873df02abf87362b8c06f
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055958"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integre registos AD AD com registos do Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Os registos do Azure Monitor permitem-lhe consultar dados para encontrar eventos específicos, analisar tendências e realizar a correlação entre várias fontes de dados. Com a integração dos registos de atividades AZure AD nos registos do Azure Monitor, pode agora executar tarefas como:

 * Compare os seus registos de login AD Azure com os registos de segurança publicados pelo Azure Security Center

 * Os estrangulamentos de desempenho da resolução de problemas na página de inserção da sua aplicação, correlacionando os dados de desempenho da aplicação a partir de Azure Application Insights.  

O vídeo que se segue numa sessão de Ignite demonstra os benefícios da utilização de registos Azure Monitor para registos AZure AD em cenários práticos do utilizador.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Neste artigo, aprende-se a integrar os registos do Azure Ative Directory (Azure AD) com o Azure Monitor.

## <a name="supported-reports"></a>Relatórios suportados

Pode encaminhar registos de atividades de auditoria e registos de atividades de login para registos do Azure Monitor para posterior análise. 

* **Registos de auditoria**: o [relatório de atividades de registos de auditoria](concept-audit-logs.md) dá-lhe acesso ao histórico de cada tarefa que é executada no seu inquilino.
* **Registos de inícios de sessão**: com o [relatório de atividades de inícios de sessão](concept-sign-ins.md), pode saber quem executou as tarefas reportadas no relatório de registos de auditoria.
* **Registos de provisionamento**: Com os [registos de provisionamento,](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics)pode monitorizar quais os utilizadores que foram criados, atualizados e eliminados em todas as suas aplicações de terceiros. 

> [!NOTE]
> A auditoria relacionada com B2C e os registos de atividades de inícios de sessão não são suportados atualmente.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para utilizar esta funcionalidade, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [inscrever-se para obter uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um inquilino do Azure AD.
* Um utilizador que seja *administrador global* ou *administrador de segurança* do inquilino do Azure AD.
* Um espaço de trabalho Log Analytics na sua subscrição Azure. Saiba como [criar um espaço de trabalho Log Analytics.](../../azure-monitor/learn/quick-create-workspace.md)

## <a name="licensing-requirements"></a>Requisitos de licenciamento

A utilização desta funcionalidade requer uma licença Azure AD Premium P1 ou P2. Para encontrar a licença certa para os seus requisitos, veja [Comparação das funcionalidades disponíveis geralmente das edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Enviar registos para O Monitor Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione as definições de diagnóstico **do diretório ativo Azure**  >  **Diagnostic settings**  ->  **Adicione a definição de diagnóstico**. Também pode selecionar Definições de **Exportação** a partir da página **'Registos** de Auditoria' ou **'Iniciar sessão'** para aceder à página de configuração de definições de diagnóstico.  
    
3. No menu **de definições de Diagnóstico,** selecione a caixa de verificação **do espaço de trabalho Enviar para registar análise** e, em seguida, selecione **Configure**.

4. Selecione o espaço de trabalho Log Analytics para onde pretende enviar os registos ou criar um novo espaço de trabalho na caixa de diálogo fornecida.  

5. Escolha uma ou ambas as opções abaixo:
    * Para enviar registos de auditoria para o espaço de trabalho Do Log Analytics, selecione a caixa de verificação **AuditLogs.** 
    * Para enviar registos de entrada no espaço de trabalho Do Log Analytics, selecione a caixa de verificação **SignInLogs.**

6. Selecione **Guardar** para guardar a definição.

    ![Definições de diagnósticos](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Após cerca de 15 minutos, verifique se os eventos são transmitidos para o seu espaço de trabalho Log Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Analise os registos de atividades da AZure com registos do Monitor Azure](howto-analyze-activity-logs-log-analytics.md)
* [Instale e utilize as vistas de análise de registo para o Azure Ative Directory](howto-install-use-log-analytics-views.md)
