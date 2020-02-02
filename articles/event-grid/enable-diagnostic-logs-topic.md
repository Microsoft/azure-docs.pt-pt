---
title: Grelha de Eventos Azure - Ativar registos de diagnóstico para um tópico
description: Este artigo fornece instruções passo a passo sobre como ativar registos de diagnóstico para um tópico de grelha de eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960506"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Registos de diagnóstico para um tópico da grelha de eventos Azure
As definições de diagnóstico permitem que os utilizadores da Rede de Eventos capturem e vejam registos de falhas de publicação e entrega num dos seguintes locais: uma conta de armazenamento Azure, um hub de eventos ou um espaço de trabalho de Log Analytics. Este artigo fornece instruções passo a passo para ativar registos de diagnóstico para um tópico de grelha de eventos.

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grelha de eventos provisionado
- Um destino previsto para a captura de registos de diagnóstico. Pode ser um dos seguintes destinos:
    - Conta de armazenamento do Azure
    - Hub de eventos
    - Área de trabalho do Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Passos para ativar registos de diagnóstico para um tópico

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para o tópico da grelha de eventos para o qual pretende ativar as definições de registo de diagnóstico. 
3. Selecione **definições** de diagnóstico sob **monitorização** no menu esquerdo.
4. Na página de **definições** de Diagnóstico, selecione **Adicionar Nova Definição de Diagnóstico**. 
    
    ![Adicione o botão de definição de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a definição de diagnóstico. 

    ![Definições de diagnóstico - nome](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Ative um ou mais destinos de captura para os registos e, em seguida, configurá-los selecionando um recurso de captura criado anteriormente. 
    - Se selecionar o **Arquivo para uma conta**de armazenamento , selecione a conta de armazenamento - **Configure,** e, em seguida, selecione a conta de armazenamento na sua subscrição Azure. 

        ![Arquivo para uma conta de armazenamento Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se selecionar o Stream para um hub de **eventos**, selecione **o hub de eventos - Configure,** e, em seguida, selecione o espaço de nome do Event Hubs, o centro de eventos e a política de acesso. 
        ![Stream para um centro de eventos](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se selecionar **Enviar para Registar Análises,** selecione o espaço de trabalho do Log Analytics.
        ![Enviar para log analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selecione as opções de Falhas de **Entrega** e **De publicação** na secção **Registo.** 
    ![Selecione as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selecione **Guardar**. Selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página de definições de **Diagnóstico,** confirme se vê uma nova entrada na tabela definições de **diagnóstico.** 
    ![Definição de diagnóstico na lista](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Também pode ativar a recolha de todas as métricas para o tema. 

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, poste seu problema no [Fórum de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
