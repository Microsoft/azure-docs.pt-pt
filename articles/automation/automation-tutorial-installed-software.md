---
title: Descubra que software está instalado nos seus VMs com Automação Azure / Microsoft Docs
description: Este artigo descreve o software instalado em VMs em todo o seu ambiente.
services: automation
keywords: inventário, automação, alteração do rastreio
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: d4acecbc6d1a1d7f617b0da95da1b97dc5a3dd75
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743675"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Descubra que software está instalado nos seus VMs

Neste tutorial, aprende-se a utilizar a funcionalidade de Rastreio e Inventário de Alterações de Automação Azure para saber que software está instalado no seu ambiente. Pode recolher e visualizar o inventário para software, ficheiros, daemons Linux, serviços Windows e chaves de registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o Controlo de Alterações e Inventário
> * Ativar um VM Azure
> * Ativar um VM não-Azure
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [conta de Automação](automation-offering-get-started.md) para manter os cadernos de observadores e de ação e a tarefa do Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para ativar a funcionalidade.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

Primeiro, tem de ativar o Controlo de alterações e Inventário para este tutorial. Se já ativou a funcionalidade anteriormente, este passo não é necessário.

>[!NOTE]
>Se os campos estiverem acinzentados, outra funcionalidade de Automação está ativada para o VM, e deve utilizar o mesmo espaço de trabalho e conta de Automação.

1. Navegue para a sua conta de Automação e selecione **Rastreio de Inventário** ou **Alteração** em Gestão de **Configuração**.

2. Escolha o espaço de trabalho [log Analytics.](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) Este espaço de trabalho recolhe dados que são gerados por funcionalidades como O Rastreio de Alterações e Inventário. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Selecione a conta Automação para utilizar.

4. Configure a localização para a implantação.

5. Clique **em ativar** para implementar a funcionalidade para o seu VM. 

    ![Banner de configuração de inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Durante a configuração, o VM é aprovisionado com o agente Log Analytics para Windows e um Trabalhador híbrido do livro de [corridas](automation-hybrid-runbook-worker.md). Ativar o Rastreio de Alterações e O Inventário pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.

Após a ativação da funcionalidade, as informações sobre o software instalado e as alterações nos fluxos vm para os registos do Monitor Azure. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Adicione um VM Azure para alterar rastreio e inventário

1. Na sua conta de Automação, navegue para **Inventário** ou **Alterar o rastreio** em Gestão de **Configuração**.

2. Selecione **+ Adicionar Azure VM**.

3. Selecione o seu VM na lista de VMs. 

4. Clique em **ativar** o rastreio de alterações e o inventário no VM. O agente Log Analytics para Windows é implantado no VM e configura o VM para falar com o espaço de trabalho log Analytics. A operação de instalação pode demorar alguns minutos. 

5. Neste ponto, se desejar, pode selecionar um novo VM da lista para ativar a funcionalidade.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Adicione uma máquina não-Azure para alterar rastreio e inventário

Para ativar máquinas não-Azure para a função:

1. Instale o [agente Log Analytics para Windows](../azure-monitor/platform/agent-windows.md) ou agente Log Analytics para o [Linux,](automation-linux-hrw-install.md)dependendo do seu sistema operativo. 

2. Navegue para a sua conta de Automação e vá ao **Rastreio de Inventário** ou **Alteração** sob Gestão de **Configuração**. 

3. Clique em **Gerir Máquinas**. É apresentada uma lista das máquinas que reportam ao seu espaço de trabalho Log Analytics que não têm o Rastreio de Alterações e Inventário ativado. Selecione a opção adequada para o seu ambiente:

    * **Ativar em todas as máquinas disponíveis** - Esta opção permite que a funcionalidade em todas as máquinas que reportem ao seu espaço de trabalho Log Analytics neste momento.
    * **Ativar em todas as máquinas e futuras máquinas disponíveis** - Esta opção permite que a funcionalidade em todas as máquinas que reportem ao seu espaço de trabalho log Analytics e, posteriormente, em todas as futuras máquinas adicionadas ao espaço de trabalho.
    * **Ativar as máquinas selecionadas** - Esta opção permite a funcionalidade apenas nas máquinas que selecionou.

    ![Gerir Máquinas](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Ver software instalado

Uma vez ativada a funcionalidade de Rastreio e Inventário de Alterações, pode visualizar os resultados na página de Inventário.

1. Na sua conta de Automação, selecione **Inventário** sob Gestão de **Configuração**.

2. Na página Inventário, clique no separador **Software**.

3. Reparenuma tabela que lista o software que foi encontrado. O software é agrupado por nome de software e versão. Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome do software, a versão, o editor, a hora da última atualização (a hora de atualização mais recente comunicada por um computador no grupo) e os computadores (a contagem de computadores com esse software).

    ![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

4. Clique numa linha para ver as propriedades do registo de software e os nomes dos computadores com esse software.

5. Para pesquisar um software específico ou um grupo de software, pode pesquisar na caixa de texto diretamente acima da lista de software.
O filtro permite-lhe pesquisar com base no nome do software, na versão ou no editor. Por exemplo, a procura de **Contoso** devolve todo o software com um nome, editor ou versão contendo **Contoso.**

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar registos de inventário do software instalado

Alterar o Rastreio e o Inventário gera dados de registo que são enviados para os registos do Monitor Azure. Para pesquisar os registos executando consultas, selecione **Log Analytics** no topo da página Inventário. Os dados de inventário são armazenados sob o tipo `ConfigurationData` .

O seguinte exemplo Log Analytics consulta devolve os resultados do inventário para a editora Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo nos registos do Monitor Azure, consulte os [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Consulte o inventário de software para uma única máquina

Para ver o inventário de software de uma única máquina, pode aceder ao Inventário a partir da página de recursos Do Azure VM ou utilizar registos do Monitor Azure para filtrar até à máquina correspondente. O seguinte exemplo Log Analytics consulta devolve a lista de software para uma máquina chamada **ContosoVM**.

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

Continue a ver com a visão geral da funcionalidade De Rastreio e Inventário de Alterações para saber mais sobre o mesmo.

> [!div class="nextstepaction"]
> [Alterar visão geral do rastreio e do inventário](change-tracking.md)
