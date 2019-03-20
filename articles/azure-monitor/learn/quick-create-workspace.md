---
title: Criar uma área de trabalho do Log Analytics no Portal do Azure | Documentos da Microsoft
description: Saiba como criar uma área de trabalho do Log Analytics para ativar a recolha de dados e soluções de gerenciamento de seus ambientes na cloud e no local no portal do Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: a68e40b7b1caf184fabb5df62d8b461fa2fa11e2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834477"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar uma área de trabalho do Log Analytics no portal do Azure
Utilize o **áreas de trabalho do Log Analytics** menu para criar uma área de trabalho do Log Analytics no portal do Azure. Uma área de trabalho do Log Analytics é um ambiente exclusivo para dados de registo do Azure Monitor. Cada área de trabalho tem seu próprio repositório de dados e a configuração e origens de dados e as soluções são configuradas para armazenar seus dados numa área de trabalho específica. Exigir uma área de trabalho do Log Analytics se pretende recolher dados das seguintes origens:

* Recursos do Azure na sua subscrição
* Computadores monitorizados pelo System Center Operations Manager no local
* Coleções de dispositivos do System Center Configuration Manager 
* Dados de diagnóstico ou de registo do armazenamento do Azure

Para outras origens, como as VMs do Azure e o Windows ou VMs do Linux no seu ambiente, consulte os seguintes tópicos:

*  [Recolher dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md) 
*  [Recolher dados de computador com Linux híbrida](../learn/quick-collect-linux-computer.md)
*  [Recolher dados do computador de Windows híbrida](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **áreas de trabalho do Log Analytics**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique em **adicionar**e, em seguida, selecione opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. 
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **grupo de recursos**, optar por utilizar um recurso existente já grupo configuração ou criar um novo.  
   * Selecione um disponíveis **localização**.  Para obter mais informações, veja em que [regiões do Log Analytics está disponível](https://azure.microsoft.com/regions/services/) e procure o Azure Monitor e a partir do **pesquisa de um produto** campo.  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição existente criada antes 2 de Abril de ou para uma subscrição que estava associada a uma inscrição de Enterprise Agreement (EA) já existente, selecione o escalão de preço preferido.  Para obter mais informações sobre os escalões específicos, consulte [detalhes de preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar o painel de recursos do Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de dar as informações necessárias no painel **Área de Trabalho do Log Analytics**, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="next-steps"></a>Passos Seguintes
Agora que tem uma área de trabalho disponível, pode configurar a recolha de monitorização de telemetria, executar pesquisas de registos para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e informações de análise. 

* Para ativar a recolha de dados dos recursos do Azure com o diagnóstico do Azure ou o armazenamento do Azure, veja [registos do serviço de recolha do Azure e as métricas de utilização do Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Adicionar o System Center Operations Manager como uma origem de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o grupo de gestão do Operations Manager e o armazenamos em sua área de trabalho do Log Analytics. 
* Ligue-se [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Reveja os [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução da sua área de trabalho.
