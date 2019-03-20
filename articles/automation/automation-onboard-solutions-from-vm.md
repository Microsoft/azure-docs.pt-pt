---
title: Carregar soluções de gestão de atualizações, controlo de alterações e inventário a partir de uma VM do Azure
description: Saiba como para carregar uma máquina virtual do Azure com soluções de gestão de atualizações, controlo de alterações e inventário que fazem parte da automatização do Azure.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 70d9957ae5f0ec43269d371c96e3722e52edb26d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837767"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Carregar soluções de gestão de atualizações, controlo de alterações e inventário da máquina virtual do Azure

A automatização do Azure fornece soluções para o ajudar a gerir as atualizações de segurança do sistema operativo, controlar as alterações e inventário o que é instalado nos seus computadores. Existem várias formas de carregar máquinas. Pode integrar a solução a partir de uma máquina virtual, [da sua conta de automatização](automation-onboard-solutions-from-automation-account.md), [a partir de várias máquinas de navegação](automation-onboard-solutions-from-browse.md), ou utilizando um [runbook](automation-onboard-solutions.md). Este artigo abrange a inclusão destas soluções a partir de uma máquina virtual do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-the-solutions"></a>Ativar as soluções

Ir para uma máquina virtual existente. Sob **OPERATIONS**, selecione **gestão de atualizações**, **inventário**, ou **controlo de alterações**. A máquina virtual pode existir em qualquer região, independentemente da localização da sua conta de automatização.

Para ativar a solução para apenas a VM, certifique-se de que **ativar para esta VM** está selecionada. Para carregar várias máquinas para a solução, selecione **ativar para as VMs nesta subscrição**e, em seguida, selecione **clique para selecionar máquinas para ativar**. Para saber como para carregar várias máquinas ao mesmo tempo, veja [soluções de integrar a gestão de atualizações, controlo de alterações e inventário](automation-onboard-solutions-from-automation-account.md).

Selecione a área de trabalho do Log Analytics do Azure e a conta de automatização e, em seguida, selecione **ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

![Integrar a solução de gestão de atualizações](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Vá para as outras soluções e, em seguida, selecione **ativar**. A área de trabalho do Log Analytics e listas de lista pendente de conta de automatização estão desativadas porque estas soluções utilizam a mesma área de trabalho e a conta de automatização, como a solução anteriormente ativada.

> [!NOTE]
> **Controlo de alterações** e **inventário** utilizar a mesma solução. Quando uma destas soluções está ativada, o outro também está ativado.

## <a name="scope-configuration"></a>Configuração de âmbito

Cada solução utiliza uma configuração de âmbito na área de trabalho para os computadores que obtém a solução de destino. A configuração de âmbito é um grupo de um ou mais pesquisas guardadas, que são utilizadas para limitar o âmbito da solução para computadores específicos. Para acessar as configurações de âmbito, na sua conta de automatização, em **recursos relacionados**, selecione **área de trabalho**. Na área de trabalho, sob **ORIGENS de dados de área de trabalho**, selecione **configurações de âmbito**.

Se a área de trabalho selecionada ainda não tiver as soluções de gestão de atualizações ou controlo de alterações, são criadas as seguintes configurações de escopo:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se a área de trabalho selecionada já tem a solução, a solução não está implementada novamente e a configuração de âmbito não for adicionada.

Selecione as reticências (**...** ) em qualquer uma das configurações e, em seguida, selecione **editar**. Na **Editar configuração de âmbito** painel, selecione **selecionar grupos de computadores**. O **grupos de computadores** painel mostra as pesquisas guardadas que são utilizadas para criar a configuração de âmbito.

## <a name="saved-searches"></a>Pesquisas guardadas

Quando é adicionado um computador para a gestão de atualizações, controlo de alterações ou soluções de inventário, o computador é adicionado a uma das duas pesquisas guardadas na sua área de trabalho. As pesquisas guardadas são consultas que contêm os computadores que são visados por estas soluções.

Vá para a área de trabalho. Sob **gerais**, selecione **pesquisas guardadas**. As duas pesquisas guardadas, que são utilizadas por estas soluções são mostradas na tabela a seguir:

|Name     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione qualquer um das pesquisas guardadas para ver a consulta que é utilizada para preencher o grupo. A imagem seguinte mostra a consulta e os resultados:

![Pesquisas guardadas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Desassociar área de trabalho

As seguintes soluções são dependentes de uma área de trabalho do Log Analytics:

* [Gestão de Atualizações](automation-update-management.md)
* [Monitorização de Alterações](automation-change-tracking.md)
* [Iniciar/parar VMs fora do horário comercial](automation-solution-vm-management.md)

Se decidir que já não pretende integrar a sua conta de automatização com uma área de trabalho do Log Analytics, pode desassociar a sua conta diretamente a partir do portal do Azure.  Antes de continuar, tem primeiro de remover as soluções mencionadas anteriormente, caso contrário, este processo será impedido de prosseguir. Reveja o artigo para a solução específica que importou para compreender os passos necessários para removê-lo.

Depois de remover estas soluções, pode executar os seguintes passos para desassociar a conta de automatização.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitorização de SQL do Azure podem ter criado os recursos de automatização e também poderão ter de ser removida antes de desassociar a área de trabalho.

1. A partir do portal do Azure, abra sua conta de automatização e na Automação de conta de página select **ligado área de trabalho** na secção **recursos relacionados** à esquerda.

1. Na página de área de trabalho de desassociar, clique em **desassociar área de trabalho**.

   ![Desassociar a página de área de trabalho](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Irá receber um pedido de confirmação de que pretende continuar.

1. Enquanto a automatização do Azure tenta desassociar a conta de sua área de trabalho do Log Analytics, pode acompanhar o progresso em **notificações** no menu.

Se utilizou a solução de gestão de atualizações, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Agenda de atualização - cada terão nomes que coincidem com as implementações de atualização que criou)

* Grupos de trabalho híbrida criados para a solução - cada um terá o nome da mesma forma para 9-4051-b6b3-227600d715c8 de machine1.contoso.com_9ceb8108 - 26 c).

Se utilizou a iniciar/parar VMs durante a solução de horário comercial, opcionalmente, pode querer remover os seguintes itens que já não são necessários depois de remover a solução.

* Iniciar e parar agendas de runbook VM
* Iniciar e parar runbooks VM
* Variáveis

## <a name="next-steps"></a>Passos Seguintes

Avance para os tutoriais para as soluções saber como utilizá-los:

* [Tutorial - Gerir atualizações para a sua VM](automation-tutorial-update-management.md)
* [Tutorial - identificar o software numa VM](automation-tutorial-installed-software.md)
* [Tutorial - alterações numa VM de resolução de problemas](automation-tutorial-troubleshoot-changes.md)
