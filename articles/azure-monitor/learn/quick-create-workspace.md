---
title: Criar uma área de trabalho do Log Analytics no Portal do Azure | Documentos da Microsoft
description: Saiba como criar uma área de trabalho do Log Analytics para ativar a recolha de dados e soluções de gerenciamento de seus ambientes na cloud e no local no portal do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656284"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar uma área de trabalho do Log Analytics no portal do Azure
Utilize o menu de espaços de **trabalho Log Analytics** para criar um espaço de trabalho log Analytics utilizando o portal Azure. Um espaço de trabalho log Analytics é um ambiente único para dados de registo do Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório e configuração de dados, e fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho. Necessita de um espaço de trabalho de Log Analytics se pretender recolher dados das seguintes fontes:

* Recursos do Azure na sua subscrição
* Computadores monitorizados pelo System Center Operations Manager no local
* Coleções de dispositivos do Gestor de Configuração 
* Dados de diagnóstico ou de registo do armazenamento do Azure

Para outras origens, como as VMs do Azure e o Windows ou VMs do Linux no seu ambiente, consulte os seguintes tópicos:

*  [Recolher dados de máquinas virtuais Azure](../learn/quick-collect-azurevm.md) 
*  [Recolher dados do computador híbrido Linux](../learn/quick-collect-linux-computer.md)
*  [Recolher dados do computador híbrido windows](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique em **Adicionar**e, em seguida, selecione escolhas para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. Este nome deve ser globalmente único em todas as subscrições do Azure Monitor.
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **o Grupo de Recursos,** opte por utilizar um grupo de recursos já configurado ou crie um novo.  
   * Selecione um **Local**disponível .  Para mais informações, consulte em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/) e procure o Monitor Azure a partir do campo **de pesquisa de um produto.**  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar uma área de trabalho para uma subscrição existente criada antes 2 de Abril de ou para uma subscrição que estava associada a uma inscrição de Enterprise Agreement (EA) já existente, selecione o escalão de preço preferido.  Para obter mais informações sobre os diferentes níveis, consulte os detalhes de preços do [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar lâmina de recursos Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de dar as informações necessárias no painel **Área de Trabalho do Log Analytics**, clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma área de trabalho disponível, pode configurar a recolha de monitorização de telemetria, executar pesquisas de registos para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e informações de análise. 

* Para permitir a recolha de dados a partir de recursos Azure com armazenamento Azure Diagnostics ou Azure, consulte [Recolher registos e métricas de serviço azure para utilização em Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Adicione o Gestor de [Operações do System Center como fonte de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o seu grupo de gestão de Gestor de Operações e armazená-lo no seu espaço de trabalho Log Analytics. 
* Ligue o Gestor de [Configuração](../platform/collect-sccm.md) para importar computadores que sejam membros de coleções na hierarquia.  
* Reveja as [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
