---
title: Criar um espaço de trabalho log Analytics no Portal Azure Microsoft Docs
description: Saiba como criar um espaço de trabalho Log Analytics para permitir soluções de gestão e recolha de dados a partir dos ambientes da sua nuvem e ambientes no portal Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: f86cc5e3edc659a233066fcf2544ed4a5019d0f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324289"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar uma área de trabalho do Log Analytics no portal do Azure
Utilize o menu **de espaços de trabalho Log Analytics** para criar um espaço de trabalho Log Analytics utilizando o portal Azure. Um espaço de trabalho Log Analytics é um ambiente único para os dados de registo do Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho. Necessita de um espaço de trabalho Log Analytics se pretender recolher dados a partir das seguintes fontes:

* Recursos Azure na sua subscrição
* Computadores no local monitorizados pelo Gestor de Operações do Centro de Sistema
* Coleções de dispositivos do Gestor de Configuração 
* Diagnósticos ou dados de registo do armazenamento da Azure

Para outras fontes, tais como VMs Azure e Windows ou VMs Linux no seu ambiente, consulte os seguintes tópicos:

*  [Recolher dados de máquinas virtuais Azure](./quick-collect-azurevm.md) 
*  [Recolher dados do computador híbrido Linux](./quick-collect-linux-computer.md)
*  [Recolher dados do computador híbrido Windows](quick-collect-windows-computer.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log analytics espaços de trabalho**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique **em Adicionar**e, em seguida, selecione escolhas para os seguintes itens:

   * Indique um nome para a nova **Área de trabalho do Log Analytics**, como *DefaultLAWorkspace*. Este nome deve ser globalmente único em todas as subscrições do Azure Monitor.
   * Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   * Para **o Grupo de Recursos,** opte por utilizar um grupo de recursos já existente configurado ou crie um novo.  
   * Selecione uma **localização disponível.**  Para obter mais informações, consulte em que [regiões o Log Analytics está disponível](https://azure.microsoft.com/regions/services/) e procure o Azure Monitor a partir do campo de **pesquisa de um** produto.  
   * Se estiver a criar uma área de trabalho numa subscrição nova criada depois de 2 de abril de 2018, a mesma utilizará automaticamente o plano de preços *Por GB* e a opção para selecionar um escalão de preços não estará disponível.  Se estiver a criar um espaço de trabalho para uma subscrição existente criada antes de 2 de abril, ou a uma subscrição que estava ligada a uma inscrição existente no Enterprise Agreement (EA), selecione o seu nível de preços preferido.  Para obter mais informações sobre os níveis específicos, consulte [Detalhes de preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar lâmina de recurso Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de fornecer as informações necessárias no painel **de trabalho do Log Analytics,** clique em **OK**.  

Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="troubleshooting"></a>Resolução de problemas
Quando cria um espaço de trabalho que foi eliminado nos últimos 14 dias e em [estado de eliminação suave,](../platform/delete-workspace.md#soft-delete-behavior)a operação pode ter resultados diferentes dependendo da configuração do seu espaço de trabalho:
1. Se fornecer o mesmo nome do espaço de trabalho, grupo de recursos, subscrição e região como no espaço de trabalho eliminado, o seu espaço de trabalho será recuperado, incluindo os seus dados, configuração e agentes conectados.
2. Se utilizar o mesmo nome do espaço de trabalho, mas diferentes grupos de recursos, subscrição ou região, obterá um erro *Este nome do espaço de trabalho já está em uso. Por favor, experimente outro.* Para anular o soft-delete e eliminar permanentemente o seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estes passos para recuperar primeiro o espaço de trabalho e realizar a eliminação permanente:
   - [Recupere](../platform/delete-workspace.md#recover-workspace) o seu espaço de trabalho
   - [Elimine permanentemente](../platform/delete-workspace.md#permanent-workspace-delete) o seu espaço de trabalho
   - Criar um novo espaço de trabalho usando o mesmo nome do espaço de trabalho

## <a name="next-steps"></a>Passos seguintes
Agora que tem um espaço de trabalho disponível, pode configurar a recolha de telemetria de monitorização, executar pesquisas de registo para analisar esses dados e adicionar uma solução de gestão para fornecer dados adicionais e insights analíticos. 

* Para permitir a recolha de dados a partir de recursos Azure com Azure Diagnostics ou armazenamento Azure, consulte [registos e métricas de serviço Da Recolha Azure para utilização no Log Analytics](../platform/resource-logs.md#send-to-log-analytics-workspace).  
* [Adicione o Gestor de Operações do System Center como uma fonte de dados](../platform/om-agents.md) para recolher dados de agentes que reportam o seu grupo de gestão de Gestor de Operações e armazene-os no seu espaço de trabalho Log Analytics. 
* Ligue [o Gestor de Configuração](../platform/collect-sccm.md) à importação de computadores que sejam membros de coleções na hierarquia.  
* Reveja as [soluções de monitorização](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do seu espaço de trabalho.

