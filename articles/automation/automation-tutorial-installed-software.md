---
title: Descubra que software está instalado nos seus computadores com a Automatização do Azure | Microsoft Docs
description: Utilize o Inventário para descobrir que software está instalado nos computadores em todo o ambiente.
services: automation
keywords: inventário, automatização,alteração, controlo
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.subservice: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 2cce925f4b3e1acc6c93019615b81983a5c95f6f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56815897"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Descobrir que software está instalado nos seus computadores do Azure e não Azure

Neste tutorial, irá aprender a descobrir que software está instalado no seu ambiente. Pode recolher e ver o inventário do software, dos ficheiros, dos daemons do Linux, dos Serviços do Windows e das chaves do Registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar a solução
> * Carregar uma VM do Azure
> * Carregar uma VM não Azure
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta de Automatização](automation-offering-get-started.md) para reter os runbooks de observador e ação e a Tarefa de Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de alterações e Inventário

Primeiro, tem de ativar o Controlo de alterações e Inventário para este tutorial. Se ativou anteriormente a solução de **Controlo de Alterações**, este passo não é preciso.

Navegue até à sua Conta de Automatização e selecione **Inventário** em **GESTÃO DE CONFIGURAÇÃO**.

Escolha a área de trabalho e a Conta de Automatização do Log Analytics e clique em **Ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

![Faixa de configuração do carregamento de Inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Para ativar a solução, configure a localização, a área de trabalho e a Conta de Automatização do Log Analytics a utilizar e clique em **Ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e Conta de Automatização.

A área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) serve para recolher dados gerados pelas funcionalidades e serviços, como o Inventário.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.
Após a solução está ativada, informações sobre alterações e de software instalado na VM são transmitidas para registos do Azure Monitor.
Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="onboard-a-vm"></a>Carregar uma VM

Na sua Conta de Automatização, navegue para **Inventário** em **GESTÃO DE CONFIGURAÇÃO**.

Selecione **+ Adicionar VM do Azure**, esta ação abre a página **Máquinas virtuais** e permite-lhe selecionar uma VM existente da lista. Selecione a VM que pretende carregar. Na página que abre clique em **Ativar**, para ativar a solução na VM. O Agente de Gestão da Microsoft é implementado na VM e configura o agente para comunicar com a área de trabalho do Log Analytics que configurou ao ativar a solução. Este processo pode demorar alguns minutos a concluir a inclusão. Neste momento, pode selecionar uma nova VM na lista e carregar outra VM.

## <a name="onboard-a-non-azure-machine"></a>Carregar uma máquina não Azure

Para adicionar máquinas não Azure, instale o agente para [Windows](../azure-monitor/platform/agent-windows.md) ou [Linux](automation-linux-hrw-install.md), consoante o sistema operativo. Assim que o agente estiver instalado, navegue até à sua Conta de Automatização e aceda ao **Inventário** em **GESTÃO DE CONFIGURAÇÃO**. Ao clicar em **Gerir Máquinas**, verá uma lista das máquinas que relatam à sua área de trabalho do Log Analytics que não têm a solução ativada. Selecione a opção adequada para o seu ambiente.

* **Ativar em todas as máquinas disponíveis** - esta opção ativa a solução em todas as máquinas que relatam à sua área de trabalho do Log Analytics neste momento.
* **Ativar em todos os computadores disponíveis e futuros** - esta opção ativa a solução em todas as máquinas que relatam à sua área de trabalho do Log Analytics e, subsequentemente, em todas as máquinas futuras adicionadas à área de trabalho.
* **Ativar em máquinas selecionadas** - esta opção ativa a solução apenas nas máquinas selecionadas.

![Gerir Máquinas](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Ver software instalado

Depois de ativar a solução Controlo de alterações e Inventário, pode ver os resultados na página **Inventário**.

A partir da Conta de Automatização, selecione **Inventário** em **GESTÃO DE CONFIGURAÇÃO**.

Na página **Inventário**, clique no separador **Software**.

No separador **Software**, existe uma tabela que lista o software que foi encontrado. O software é agrupado por nome de software e versão.

Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome do software, a versão, o editor, a hora da última atualização (a hora de atualização mais recente comunicada por um computador no grupo) e os computadores (a contagem de computadores com esse software).

![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

Clique numa linha para ver as propriedades do registo de software e os nomes dos computadores com esse software.

Para pesquisar um software específico ou um grupo de software, pode pesquisar na caixa de texto diretamente acima da lista de software.
O filtro permite-lhe pesquisar com base no nome do software, na versão ou no editor.

Por exemplo, a pesquisa de "Contoso" devolve todos os softwares com um nome, editor ou versão que contêm "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar registos de inventário do software instalado

O inventário gera dados de registo que são enviados para os registos do Azure Monitor. Para pesquisar os registos através da execução de consultas, selecione **Log Analytics** na parte superior da janela **Inventário**.

Os dados do Inventário são armazenados abaixo do tipo **ConfigurationData**.
A seguinte consulta do Log Analytics de exemplo devolve os resultados de inventário em que o Publicador é igual a "Microsoft Corporation".

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo nos registos do Azure Monitor, consulte [registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="single-machine-inventory"></a>Inventário de computador único

Para ver o inventário de software para uma única máquina, pode aceder ao inventário a partir da página de recursos de VM do Azure ou utilize registos do Azure Monitor para filtrar apenas a máquina correspondente.
A consulta do Log Analytics de exemplo seguinte devolve a lista de software de um computador com o nome ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu como ver o inventário de software, como:

> [!div class="checklist"]
> * Ativar a solução
> * Carregar uma VM do Azure
> * Carregar uma VM não Azure
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

Avance para a descrição geral da solução Controlo de alterações e Inventário para saber mais acerca dela.

> [!div class="nextstepaction"]
> [Solução Gestão de mudança e Inventário](automation-change-tracking.md)
