---
title: Descubra que software está instalado nos seus VMs com Azure Automation Microsoft Docs
description: Este artigo descreve o software instalado em VMs em todo o seu ambiente.
services: automation
keywords: inventário, automação, rastreio de mudança
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 0c083f4576e123ea14d837ed3915c56d18d84623
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207568"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Descubra que software está instalado nos seus VMs

Neste tutorial, aprende-se a utilizar a funcionalidade de Rastreio e Inventário de Alterações da Automação Azure para saber que software está instalado no seu ambiente. Pode recolher e visualizar o inventário para software, ficheiros, daemons Linux, serviços Windows e chaves do Registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o Controlo de Alterações e Inventário
> * Ativar um VM Azure
> * Ativar um VM não-Azure
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma [conta de Automação](./index.yml) para manter os livros de observadores e de ação e a tarefa do Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para ativar a funcionalidade.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

Primeiro, tem de ativar o Controlo de alterações e Inventário para este tutorial. Se já ativou previamente a funcionalidade, este passo não é necessário.

>[!NOTE]
>Se os campos estiverem acinzentados, outra função de Automação está ativada para o VM, e deve utilizar o mesmo espaço de trabalho e conta Demómida.

1. Navegue na sua conta de Automação e selecione **o rastreio de Inventário** ou **Alteração** em **Gestão de Configuração.**

2. Escolha o espaço de trabalho [Log Analytics.](../azure-monitor/log-query/log-query-overview.md) Este espaço de trabalho recolhe dados que são gerados por funcionalidades como Change Tracking e Inventory. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Selecione a conta Automation to use.

4. Configurar a localização para a implantação.

5. Clique em **Ativar** para implementar a funcionalidade para o seu VM. 

    ![Banner de configuração de inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Durante a configuração, o VM é a provisionado com o agente Log Analytics para Windows e um [Trabalhador De Runbook Híbrido](automation-hybrid-runbook-worker.md). Ativar o Rastreio e Inventário de Alterações pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.

Após a funcionalidade estar ativada, informações sobre software instalado e alterações nos fluxos VM para registos do Azure Monitor. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Adicione um VM Azure para alterar o tracking e o inventário

1. Na sua conta de Automação, navegue para **o rastreio de Inventário** ou **Alteração** em **Gestão de Configuração.**

2. **Selecione + Adicionar Azure VM**.

3. Selecione o seu VM na lista de VMs. 

4. Clique **em Ativar** para ativar o Rastreio de Alterações e o Inventário no VM. O agente Log Analytics para Windows é implantado no VM e configura o VM para falar com o espaço de trabalho Log Analytics. A operação de instalação pode demorar alguns minutos. 

5. Neste ponto, se desejar, pode selecionar um novo VM da lista para ativar a funcionalidade.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Adicione uma máquina não-Azure para alterar o tracking e o inventário

Para ativar máquinas não-Azure para a funcionalidade:

1. Instale o [agente Log Analytics para o Windows](../azure-monitor/platform/agent-windows.md) ou o agente Log Analytics para o [Linux,](automation-linux-hrw-install.md)dependendo do seu sistema operativo. 

2. Navegue na sua conta de Automação e vá ao **rastreio de Inventário** ou **Alteração** em **Gestão de Configuração.** 

3. Clique **em Gerir Máquinas**. É-lhe apresentada uma lista das máquinas que reportam ao seu espaço de trabalho Log Analytics que não têm o 'Change Tracking and Inventory' ativado. Selecione a opção adequada para o seu ambiente:

    * **Ativar em todas as máquinas disponíveis** - Esta opção permite a funcionalidade em todas as máquinas que reportam ao seu espaço de trabalho Log Analytics neste momento.
    * **Ativar em todas as máquinas e máquinas futuras disponíveis** - Esta opção permite a funcionalidade em todas as máquinas que reportam ao seu espaço de trabalho Log Analytics e, posteriormente, em todas as máquinas futuras adicionadas ao espaço de trabalho.
    * **Ativar em máquinas selecionadas** - Esta opção permite a funcionalidade apenas nas máquinas que selecionou.

    ![Gerir Máquinas](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Ver software instalado

Uma vez ativada a função De Rastreio e Inventário de Alterações, pode ver os resultados na página 'Inventário'.

1. Na sua conta Demôm automação, **selecione Inventário** em **Gestão de Configuração.**

2. Na página Inventário, clique no separador **Software**.

3. Note uma tabela que lista o software que foi encontrado. O software é agrupado por nome de software e versão. Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome do software, a versão, o editor, a hora da última atualização (a hora de atualização mais recente comunicada por um computador no grupo) e os computadores (a contagem de computadores com esse software).

    ![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

4. Clique numa linha para ver as propriedades do registo de software e os nomes dos computadores com esse software.

5. Para pesquisar um software específico ou um grupo de software, pode pesquisar na caixa de texto diretamente acima da lista de software.
O filtro permite-lhe pesquisar com base no nome do software, na versão ou no editor. Por exemplo, a procura de **Contoso** devolve todo o software com um nome, editor ou versão contendo **Contoso.**

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar registos de inventário do software instalado

O Change Tracking and Inventory gera dados de registo que são enviados para registos do Monitor Azure. Para pesquisar os registos executando consultas, selecione **Log Analytics** no topo da página 'Inventário'. Os dados de inventário são armazenados sob o tipo `ConfigurationData` .

O seguinte exemplo Log Analytics consulta devolve os resultados do inventário para a editora Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo nos registos do Azure Monitor, consulte [os registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Consulte o inventário de software para uma única máquina

Para ver o inventário de software de uma única máquina, pode aceder ao Inventário a partir da página de recursos Azure VM ou utilizar registos do Azure Monitor para filtrar para a máquina correspondente. O seguinte exemplo Log Analytics consulta devolve a lista de software para uma máquina chamada **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial aprendeu como ver o inventário de software:

> [!div class="checklist"]
> * Ativar o Controlo de Alterações e Inventário
> * Ativar um VM Azure
> * Ativar um VM não-Azure
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

Continue a visão geral da funcionalidade De Rastreio e Inventário de Alterações para saber mais sobre o mesmo.

> [!div class="nextstepaction"]
> [Alterar rastreio e visão geral do inventário](change-tracking/overview.md)
