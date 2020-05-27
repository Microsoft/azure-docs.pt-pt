---
title: Criar um espaço de trabalho de Log Analytics no Portal Azure [ Microsoft Docs
description: Aprenda a criar um espaço de trabalho log Analytics para permitir soluções de gestão e recolha de dados a partir da sua nuvem e ambientes no local no portal Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: e6ef8fb7dfd98a509b0b89e049c70169dafbe134
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860567"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho log Analytics no portal Azure
Utilize o menu de espaços de **trabalho Log Analytics** para criar um espaço de trabalho log Analytics utilizando o portal Azure. Um espaço de trabalho log Analytics é um ambiente único para dados de registo do Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório e configuração de dados, e fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho. Necessita de um espaço de trabalho de Log Analytics se pretender recolher dados das seguintes fontes:

* Recursos Azure na sua subscrição
* Computadores no local monitorizados pelo System Center Operations Manager
* Coleções de dispositivos do Gestor de Configuração 
* Diagnósticos ou dados de registo do armazenamento do Azure

Para outras fontes, como VMs Azure e Windows ou VMs Linux no seu ambiente, consulte os seguintes tópicos:

*  [Recolher dados de máquinas virtuais Azure](../learn/quick-collect-azurevm.md) 
*  [Recolher dados do computador híbrido Linux](../learn/quick-collect-linux-computer.md)
*  [Recolher dados do computador híbrido windows](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique em **Adicionar**e, em seguida, selecione escolhas para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. Este nome deve ser globalmente único em todas as subscrições do Azure Monitor.
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **o Grupo de Recursos,** opte por utilizar um grupo de recursos já configurado ou crie um novo.  
   * Selecione um **Local**disponível .  Para mais informações, consulte em que [regiões está disponível o Log Analytics](https://azure.microsoft.com/regions/services/) e procure o Monitor Azure a partir do campo **de pesquisa de um produto.**  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar um espaço de trabalho para uma subscrição existente criada antes de 2 de abril, ou para uma subscrição que estivesse ligada a uma inscrição existente no Enterprise Agreement (EA), selecione o seu nível de preços preferido.  Para obter mais informações sobre os diferentes níveis, consulte os detalhes de preços do [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar lâmina de recursos Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de fornecer as informações necessárias no painel de trabalho de **Log Analytics,** clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="troubleshooting"></a>Resolução de problemas
Quando se cria um espaço de trabalho que foi eliminado nos últimos 14 dias e em [estado de soft-delete,](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#soft-delete-behavior)a operação pode ter resultados diferentes dependendo da configuração do espaço de trabalho:
1. Se fornecer o mesmo nome de espaço de trabalho, grupo de recursos, subscrição e região que no espaço de trabalho eliminado, o seu espaço de trabalho será recuperado, incluindo os seus dados, configuração e agentes conectados.
2. Se utilizar o mesmo nome de espaço de trabalho, mas um grupo de recursos diferentes, subscrição ou região, terá um erro *Este nome de espaço de trabalho já está em uso. Por favor, tente outro.* Para anular o soft-delete e eliminar permanentemente o seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estes passos para recuperar primeiro o espaço de trabalho e execute a eliminação permanente:
   - [Recupere](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) o seu espaço de trabalho
   - [Elimine permanentemente](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) o seu espaço de trabalho
   - Criar um novo espaço de trabalho usando o mesmo nome de espaço de trabalho

## <a name="next-steps"></a>Passos seguintes
Agora que tem um espaço de trabalho disponível, pode configurar a recolha de telemetria de monitorização, executar pesquisas de registo para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e insights analíticos. 

* Para permitir a recolha de dados a partir de recursos Azure com armazenamento Azure Diagnostics ou Azure, consulte [Recolher registos e métricas de serviço azure para utilização em Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Adicione o Gestor de [Operações do System Center como fonte de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o seu grupo de gestão de Gestor de Operações e armazená-lo no seu espaço de trabalho Log Analytics. 
* Ligue o Gestor de [Configuração](../platform/collect-sccm.md) para importar computadores que sejam membros de coleções na hierarquia.  
* Reveja as [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.
