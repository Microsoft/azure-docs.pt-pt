---
title: Gerir uma máquina virtual do Azure com a recolha de inventário | Microsoft Docs
description: Gerir uma máquina virtual com a recolha de inventário
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: inventário, automatização,alteração, controlo
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c0caece57885dfa3c26d368990a6af8e5b14eed6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823999"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerir uma máquina virtual do Azure com a recolha de inventário

Pode ativar o controlo de inventário para uma máquina virtual do Azure a partir da página de recursos da máquina virtual. Pode recolher e ver o inventário do software, dos ficheiros, dos daemons do Linux, dos Serviços do Windows e das chaves do Registo do Windows nos seus computadores. Este método fornece uma interface de utilizador baseada no browser para definir e configurar a recolha de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Este artigo pressupõe que tem uma VM para configurar a solução em. Se não tiver uma máquina virtual do Azure, [crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Ativar a recolha de inventário a partir da página de recursos da máquina virtual

1. No painel esquerdo do portal do Azure, selecione **Máquinas virtuais**.
2. Na lista de máquinas virtuais, selecione uma máquina virtual.
3. Sobre o **Resource** menu, em **operações**, selecione **inventário**.
4. Selecione uma área de trabalho do Log Analytics para armazenar os registos de dados.
    Se não existir uma área de trabalho disponível para essa região, é-lhe pedido para criar uma área de trabalho predefinida e uma conta de automatização.
5. Para iniciar a inclusão do seu computador, selecione **Ativar**.

   ![Ver opções de inclusão](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Uma barra de estado notifica-o de que a solução está a ser ativada. Este processo pode demorar até 15 minutos a concluir. Durante este período, pode fechar a janela, ou podem mantê-la aberta e notifica-o quando a solução está ativada. Pode monitorizar o estado da implementação a partir do painel de notificações.

   ![Ver a solução de inventário imediatamente após a inclusão](./media/automation-vm-inventory/inventory-onboarded.png)

Quando a implementação estiver concluída, a barra de estado desaparece. O sistema ainda está a recolher dados de inventário e os dados podem não estar visíveis. Um conjunto completo de dados pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configurar as definições de inventário

Por predefinição, o software, os serviços do Windows e os daemons Linux estão configurados para a recolha. Para recolher o registo do Windows e o inventário de ficheiros, configure as definições de recolha do inventário.

1. Na **inventário** visualizar, selecione a **editar definições de** botão na parte superior da janela.
2. Para adicionar uma nova definição de recolha, navegue para a categoria de definição que quer adicionar através dos separadores **Registo do Windows**, **Ficheiros do Windows** e **Ficheiros do Linux**.
3. Selecione a categoria adequada e clique em **adicionar** na parte superior da janela.

As tabelas seguintes fornecem informações sobre cada propriedade que pode ser configurada para as várias categorias.

### <a name="windows-registry"></a>Registo do Windows

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Chave do Registo do Windows   | O caminho para verificar o ficheiro, por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Ficheiros do Windows

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "c:\temp\myfile.txt"

### <a name="linux-files"></a>Ficheiros do Linux

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "/etc/*.conf"       |
|Tipo de Caminho     | O tipo de item a controlar, em que valores possíveis são Ficheiro e Diretório        |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Utilizar o Sudo     | Esta definição determina se o sudo é utilizado ao verificar o item.         |
|Ligações     | Esta definição determina como as ligações simbólicas são processadas ao atravessar diretórios.<br> **Ignorar** - ignora as ligações simbólicas e não inclui os ficheiros/diretórios referenciados<br>**Seguir** - segue as ligações simbólicas durante a recursão e também inclui os ficheiros/diretórios referenciados<br>**Gerir** - segue as ligações simbólicas e permite alterar o tratamento do conteúdo devolvido      |

## <a name="manage-machine-groups"></a>Gerir grupos de máquinas

Inventário permite-lhe criar e ver os grupos de computadores nos registos do Azure Monitor. Grupos de máquinas são coleções de computadores definidos por uma consulta nos registos do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para a vista de sua máquina agrupa selecione os **Machine grupos** separador na página de inventário.

![Ver grupos de máquina na página de inventário](./media/automation-vm-inventory/inventory-machine-groups.png)

Selecionar um grupo de máquina da lista abre a página de grupos de máquina. Esta página mostra detalhes sobre o grupo de máquina. Estes detalhes incluem a consulta de análise de registo que é utilizada para definir o grupo. Na parte inferior da página, é uma lista paginada das máquinas que fazem parte desse grupo.

![Ver página do grupo de máquina](./media/automation-vm-inventory/machine-group-page.png)

Clique nas **+ Clone** botão para clonar o grupo de máquina. Aqui tem de dar o grupo de um novo nome e o alias para o grupo. A definição pode ser alterada neste momento. Depois de alterar o press de consulta **consulta para validar** para pré-visualizar as máquinas que seriam selecionadas. Quando estiver satisfeito com o grupo de clique **criar** para criar o grupo de máquina

Se quiser criar um novo grupo de mchine, selecione **+ criar um grupo de máquina**. Este botão abre o **criar uma página do grupo de máquina** onde pode definir seu novo grupo. Clique em **criar** para criar o grupo.

![Criar novo grupo de máquina](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Desligar a máquina virtual da gestão

Para remover a máquina virtual da gestão de inventário:

1. No painel esquerdo do portal do Azure, selecione **Log Analytics** e, em seguida, selecione a área de trabalho que utilizou quando efetuou a inclusão da máquina virtual.
2. Na janela **Log Analytics**, no menu **Recurso**, na categoria **Origens de Dados da Área de Trabalho**, selecione **Máquinas virtuais**.
3. Na lista, selecione a máquina virtual que quer desligar. A máquina virtual tem uma marca de verificação verde junto a **Esta área de trabalho** na coluna **Ligação OMS**.

   >[!NOTE]
   >OMS agora é referido como registos do Azure Monitor.
   
4. Na parte superior da página seguinte, selecione **Desligar**.
5. Na janela de confirmação, selecione **Sim**.
    Esta ação desliga a máquina virtual da gestão.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a gestão de alterações nas definições de ficheiros e do registo nas suas máquinas virtuais, veja [Controlar as alterações de software com a solução Controlo de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre a gestão do Windows e atualizações de pacote em suas máquinas virtuais, veja [solução de gestão de atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).

