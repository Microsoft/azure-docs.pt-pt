---
title: Saiba como carregar soluções de gestão de atualizações, controlo de alterações e inventário na automatização do Azure
description: Saiba como para carregar uma Azure Virtual máquina com soluções de gestão de atualizações, controlo de alterações e inventário que fazem parte da automatização do Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c020d3c7513efda93d7ac5d3bdd79f21f8bc77dd
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818482"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Carregar soluções de gestão de atualizações, controlo de alterações e inventário

A automatização do Azure fornece soluções para gerenciar atualizações de segurança do sistema operativo, controlar as alterações e inventário o que é instalado nos seus computadores. Existem várias formas de carregar máquinas, pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), [a partir de várias máquinas de navegação](automation-onboard-solutions-from-browse.md), de sua conta de automatização ou por [runbook](automation-onboard-solutions.md). Este artigo abrange a inclusão destas soluções a partir da sua conta de automatização.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure a https://portal.azure.com

## <a name="enable-solutions"></a>Ativar soluções

Navegue até à sua conta de automatização e selecione **inventário** ou **controlo de alterações** sob **CONFIGURATION MANAGEMENT**.

Escolha a área de trabalho do Log Analytics e a conta de automatização e clique em **ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

![Carregar soluções de inventário](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Ao ativar soluções, apenas determinadas regiões são suportadas para ligar uma área de trabalho do Log Analytics e uma conta de automatização.

A tabela seguinte mostra os mapeamentos suportados:

|**Região de área de trabalho do log Analytics**|**Região de automatização do Azure**|
|---|---|
|Sudeste da Austrália.|Sudeste da Austrália.|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|

A solução de Controlo de Alterações e Inventário fornece a capacidade de [controlar as alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) nas suas máquinas virtuais. Neste passo, vai ativar a solução numa máquina virtual.

Quando a notificação de inclusão da solução de controlo de alterações e inventário estiver concluída, clique em **Gestão de Atualizações**, em **GESTÃO DE CONFIGURAÇÃO**.

A solução de Gestão de Atualizações permite gerir atualizações e correções para as VMs do Azure. Pode avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM. Esta ação ativada a solução para a sua VM.

Selecione **gestão de atualizações** sob **gestão de ATUALIZAÇÕES**. A área de trabalho do Log Analytics selecionada é a mesma área de trabalho utilizada no passo anterior. Clique em **Ativar** para integrar a solução de Gestão de atualizações. A solução demora até 15 minutos a ativar.

![Carregar atualização solução](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuração de Âmbito

Cada solução utiliza uma configuração de âmbito dentro da área de trabalho para os computadores que obtém a solução de destino. A configuração de âmbito é um grupo de um ou mais pesquisas guardadas que é utilizado para limitar o âmbito da solução para computadores específicos. Para acessar as configurações de âmbito, na sua conta de automatização sob **recursos relacionados**, selecione **área de trabalho**. Em seguida, na área de trabalho sob **ORIGENS de dados de área de trabalho**, selecione **configurações de âmbito**.

Se a área de trabalho selecionada ainda não tiver as soluções de gestão de atualizações ou controlo de alterações, são criadas as seguintes configurações de escopo:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se a área de trabalho selecionada já tem a solução, a solução não é novamente implementada e a configuração de âmbito não for adicionada a ele.

## <a name="saved-searches"></a>Pesquisas guardadas

Quando é adicionado um computador para o gerenciamento de atualizações ou as soluções de controlo de alterações e inventário, estes serão adicionados a uma das duas pesquisas guardadas na sua área de trabalho. Estas pesquisas guardadas são consultas que contêm os computadores que são visados por estas soluções.

Navegue para a sua conta de automatização e selecione **pesquisas guardadas** sob **geral**. As duas pesquisas guardadas utilizadas por estas soluções podem ser vistas na tabela a seguir:

|Name     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione qualquer pesquisa guardada para ver a consulta usada para preencher o grupo. A imagem seguinte mostra a consulta e os resultados:

![Pesquisas guardadas](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Carregar VMs do Azure

Da sua automação de conta Selecione **inventário** ou **controlo de alterações** sob **o GERENCIAMENTO de configuração**, ou **gestão de atualizações** sob **gestão de ATUALIZAÇÕES**.

Clique em **+ adicionar VMs do Azure**, selecione uma ou mais VMs a partir da lista. Máquinas virtuais que não pode ser ativadas são cinzento para fora e não pode ser selecionada. Sobre o **ativar gestão de atualizações** página, clique em **ativar**. Esta ação adiciona as VMs selecionadas para o grupo de computadores para a solução, a pesquisa guardado.

![Ativar as VMs do Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Carregar uma máquina não Azure

As máquinas não no Azure tem de ser adicionados manualmente. Da sua automação de conta Selecione **inventário** ou **controlo de alterações** sob **o GERENCIAMENTO de configuração**, ou **gestão de atualizações** sob **gestão de ATUALIZAÇÕES**.

Clique em **máquina não pertencente ao Azure de adicionar**. Esta ação abre uma nova janela do browser com o [obter instruções sobre como instalar e configurar o Microsoft Monitoring Agent no computador](../azure-monitor/platform/log-analytics-agent.md) para que a máquina pode começar a geração de relatórios para a solução. Se estiver a inclusão de uma máquina que atualmente geridos pelo System Center Operations Manager, um novo agente não é necessário, as informações da área de trabalho são inseridas no agente existente.

## <a name="onboard-machines-in-the-workspace"></a>Carregar máquinas na área de trabalho

Instalado manualmente máquinas ou máquinas já relatam à sua área de trabalho deve ser adicionada a automatização do Azure para a solução seja ativado. Da sua automação de conta Selecione **inventário** ou **controlo de alterações** sob **o GERENCIAMENTO de configuração**, ou **gestão de atualizações** sob **gestão de ATUALIZAÇÕES**.

Selecione **gerir máquinas**. Esta ação abre o **gerir máquinas** página. Esta página permite-lhe ativar a solução num conjunto selecionado de máquinas, todas as máquinas disponíveis, ou ativar a solução para todos os computadores atuais e ativá-lo em todas as máquinas futuras. O **gerir máquinas** botão poderá estar a cinzento se escolheu anteriormente a opção **ativar em todas as máquinas disponíveis e futuras**.

![Pesquisas guardadas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Todas as máquinas disponíveis

Para ativar a solução para todas as máquinas disponíveis, selecione **ativar em todas as máquinas disponíveis**. Esta ação desativa o controle para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes dos computadores a relatar à área de trabalho para o grupo de computadores guardado consulta de pesquisa. Quando selecionado, esta ação desativa a **gerir máquinas** botão.

### <a name="all-available-and-future-machines"></a>Todas as máquinas disponíveis e futuras

Para ativar a solução para todas as máquinas disponíveis e máquinas futuras, selecione **ativar em todas as máquinas disponíveis e futuras**. Esta opção elimina as pesquisas guardadas e configurações de âmbito da área de trabalho. Esta ação abre a solução para todos os do Azure e máquinas não Azure que estão a enviar relatórios para a área de trabalho. Quando selecionado, esta ação desativa a **gerir máquinas** botão permanentemente porque não existe nenhuma configuração de âmbito à esquerda.

### <a name="selected-machines"></a>Máquinas selecionadas

Para ativar a solução para uma ou mais máquinas, selecione **ativar nas máquinas selecionadas** e clique em **adicionar** junto a cada máquina que pretende adicionar à solução. Esta tarefa adiciona os nomes das máquinas selecionadas para o grupo de computadores guardado consulta de pesquisa para a solução.

## <a name="unlink-workspace"></a>Desassociar área de trabalho

As seguintes soluções são dependentes de uma área de trabalho do Log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/parar VMs fora do horário comercial](automation-solution-vm-management.md)

Se decidir que já não pretende integrar a sua conta de automatização com uma área de trabalho do Log Analytics, pode desassociar a sua conta diretamente a partir do portal do Azure.  Antes de continuar, tem primeiro de remover as soluções mencionadas anteriormente, caso contrário, este processo será impedido de prosseguir. Reveja o artigo para a solução específica, que importar para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode concluir os passos seguintes para desassociar a conta de automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização de SQL do Azure podem ter criado os recursos de automatização e também poderão ter de ser removida antes de desassociar a área de trabalho.

1. A partir do portal do Azure, abra sua conta de automatização e na Automação de conta de página select **ligado área de trabalho** sob a seção rotulada **recursos relacionados** à esquerda.

2. Na página de área de trabalho de desassociar, clique em **desassociar área de trabalho**.

   ![Desassociar a página de área de trabalho](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Receberá um aviso a confirmar que pretende continuar.

3. Enquanto a automatização do Azure tenta desassociar a conta de sua área de trabalho do Log Analytics, pode acompanhar o progresso em **notificações** no menu.

Se utilizou a solução de gestão de atualizações, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Agenda de atualização - cada terão nomes que coincidem com as implementações de atualização que criou)

* Grupos de trabalho híbrida criados para a solução - cada um terá o nome da mesma forma para 9-4051-b6b3-227600d715c8 de machine1.contoso.com_9ceb8108 - 26 c).

Se utilizou a iniciar e parar VMs durante a solução de horário comercial, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar agendas de runbook VM
* Iniciar e parar runbooks VM
* Variáveis

## <a name="next-steps"></a>Passos Seguintes

Avance para os tutoriais sobre as soluções para saber como utilizá-los.

* [Tutorial - Gerir atualizações para a sua VM](automation-tutorial-update-management.md)

* [Tutorial - identificar o software numa VM](automation-tutorial-installed-software.md)

* [Tutorial - alterações numa VM de resolução de problemas](automation-tutorial-troubleshoot-changes.md)
