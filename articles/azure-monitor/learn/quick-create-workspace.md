---
title: Criar uma área de trabalho do Log Analytics no Portal do Azure | Documentos da Microsoft
description: Saiba como criar uma área de trabalho do Log Analytics para ativar a recolha de dados e soluções de gerenciamento de seus ambientes na cloud e no local no portal do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: bc8162a157cee10deeb8bf3f1e62a53fbdd30d0e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513444"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar uma área de trabalho do Log Analytics no portal do Azure
Use o menu **log Analytics espaços de trabalho** para criar um espaço de trabalho do log Analytics usando o portal do Azure. Um espaço de trabalho Log Analytics é um ambiente exclusivo para dados de Azure Monitor log. Cada espaço de trabalho tem seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar seus dados em um espaço de trabalho específico. Você precisará de um espaço de trabalho Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure na sua subscrição
* Computadores monitorizados pelo System Center Operations Manager no local
* Coleções de dispositivos de Configuration Manager 
* Dados de diagnóstico ou de registo do armazenamento do Azure

Para outras origens, como as VMs do Azure e o Windows ou VMs do Linux no seu ambiente, consulte os seguintes tópicos:

*  [Recolher dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md) 
*  [Recolher dados de computador com Linux híbrida](../learn/quick-collect-linux-computer.md)
*  [Recolher dados do computador de Windows híbrida](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar áreas de trabalho
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique em **Adicionar**e selecione opções para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. Esse nome deve ser globalmente exclusivo em todas as assinaturas de Azure Monitor.
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **grupo de recursos**, optar por utilizar um recurso existente já grupo configuração ou criar um novo.  
   * Selecione um disponíveis **localização**.  Para obter mais informações, consulte quais [regiões log Analytics está disponível no](https://azure.microsoft.com/regions/services/) e procure por Azure monitor no campo **Pesquisar um produto** .  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição existente criada antes 2 de Abril de ou para uma subscrição que estava associada a uma inscrição de Enterprise Agreement (EA) já existente, selecione o escalão de preço preferido.  Para obter mais informações sobre os escalões específicos, consulte [detalhes de preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar Log Analytics folha de recursos](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de dar as informações necessárias no painel **Área de Trabalho do Log Analytics**, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma área de trabalho disponível, pode configurar a recolha de monitorização de telemetria, executar pesquisas de registos para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e informações de análise. 

* Para ativar a recolha de dados dos recursos do Azure com o diagnóstico do Azure ou o armazenamento do Azure, veja [registos do serviço de recolha do Azure e as métricas de utilização do Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Adicionar o System Center Operations Manager como uma origem de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o grupo de gestão do Operations Manager e o armazenamos em sua área de trabalho do Log Analytics. 
* Ligue-se [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
