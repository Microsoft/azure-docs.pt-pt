---
title: Tutorial - Stream logs para um centro de eventos Azure | Microsoft Docs
description: Saiba como configurar o Azure Diagnostics para empurrar os registos do Azure Ative Directory para um centro de eventos
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0443dcb2bf3bd58f2474c507c9f9594fb6d8a7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469189"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Tutorial: Stream Azure Ative Directory registra-se para um centro de eventos Azure

Neste tutorial, você aprende a configurar as definições de diagnóstico do Azure Monitor para transmitir os registos do Azure Ative Directory (Azure AD) para um centro de eventos Azure. Utilize este mecanismo para integrar os seus registos com ferramentas de Gestão de Informações e Eventos de Segurança (SIEM) de terceiros, como Splunk e QRadar.

## <a name="prerequisites"></a>Pré-requisitos 

Para utilizar esta funcionalidade, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [inscrever-se para obter uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um inquilino do Azure AD.
* Um utilizador que seja *administrador global* ou *administrador de segurança* do inquilino do Azure AD.
* Um espaço de nomes dos Hubs de Eventos e um hub de eventos na sua subscrição do Azure. Saiba como [criar um hub de eventos](../../event-hubs/event-hubs-create.md).

## <a name="stream-logs-to-an-event-hub"></a>Registos de fluxo para um centro de eventos

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione registos de auditoria de monitorização **do diretório ativo Azure**  >    >  . 

3. Selecione **Exportar Definições**.  
    
4. No painel **Definições de diagnósticos**, escolha uma das opções abaixo:
    * Para alterar as definições existentes, selecione **Editar definição**.
    * Para adicionar definições novas, selecione **Adicionar definição de diagnósticos**.  
      Pode ter até três definições.

      ![Exportar definições](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Assinale a caixa de verificação **Transmitir para um hub de eventos** e selecione **Hub de Eventos/Configurar**.

6. Selecione a subscrição do Azure e o espaço de nomes dos Hubs de Eventos para onde pretende encaminhar os registos.  
    A subscrição e o espaço de nomes dos Hubs de Eventos devem ambos ser associados ao inquilino do Azure AD de onde os registos são transmitidos. Também pode especificar um hub de eventos no espaço de nomes dos Hubs de Eventos para onde os registos devem ser enviados. Se não for especificado nenhum hub de eventos, é criado um no espaço de nomes com o nome predefinido **insights-logs-audit**.

7. Selecione **OK** para sair da configuração do hub de eventos.

8. Escolha uma ou ambas as opções abaixo:
    * Para enviar registos de auditoria para o centro de eventos, selecione a caixa de verificação **AuditLogs.** 
    * Para enviar registos de entrada no centro de eventos, selecione a caixa de verificação **SignInLogs.**

9. Selecione **Guardar** para guardar a definição.

    ![Definições de diagnóstico](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Depois de cerca de 15 minutos, verifique se os eventos são apresentados no seu hub de eventos. Para tal, aceda ao hub de eventos a partir do portal e confirme que a contagem das **mensagens de entrada** é superior a zero. 

    ![Registos de auditoria](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Aceder a dados a partir do seu hub de eventos

Quando os dados forem apresentados no hub de eventos, pode aceder e lê-los de duas formas:

* **Configurar uma ferramenta SIEM suportada**. Para ler os dados do hub de eventos, a maioria das ferramentas precisa da cadeia de ligação do hub de eventos e de determinadas permissões para a sua subscrição do Azure. As ferramentas de terceiros com integração do Azure Monitor incluem, entre outras:
    
    * **ArcSight**: Para obter mais informações sobre a integração dos registos AD do Azure com o Splunk, consulte [os registos do Diretório Ativo do Azure com o ArcSight utilizando o Azure Monitor](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk**: para obter mais informações sobre como integrar registos do Azure AD no Splunk, veja [Integrate Azure AD logs with Splunk by using Azure Monitor](./howto-integrate-activity-logs-with-splunk.md) (Utilizar o Azure Monitor para integrar registos do Azure AD no Splunk).
    
    * **IBM QRadar**: o DSM e o Protocolo dos Hubs de Eventos do Azure estão disponíveis para transferência no [suporte da IBM](https://www.ibm.com/support). Para obter mais informações sobre a integração no Azure, aceda ao site [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic**: para configurar a Sumo Logic para consumir dados a partir de um hub de eventos, veja [Instalar a aplicação do Azure AD e ver os dashboards](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards). 

* **Configurar ferramentas personalizadas**. Se a sua SIEM atual ainda não for suportada nos diagnóstico do Azure Monitor, pode configurar as ferramentas personalizadas com a API dos Hubs de Eventos. Para saber mais, veja [Começar a receber mensagens a partir de um hub de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).


## <a name="next-steps"></a>Passos seguintes

* [Integre os registos do Azure Ative Directory com o ArcSight utilizando o Azure Monitor](howto-integrate-activity-logs-with-arcsight.md)
* [Integrate Azure AD logs with Splunk by using Azure Monitor](./howto-integrate-activity-logs-with-splunk.md) (Utilizar o Azure Monitor para integrar registos do Azure AD no Splunk)
* [Integrate Azure AD logs with Splunk by using Azure Monitor](howto-integrate-activity-logs-with-sumologic.md) (Utilizar o Azure Monitor para integrar registos do Azure AD no SumoLogic)
* [Interpretar esquema de registos de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
