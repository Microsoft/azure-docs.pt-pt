---
title: Gerir a recolha de inventário da Azure Automation a partir de VMs [ Microsoft Docs
description: Este artigo diz como gerir a recolha de inventário de VMs.
services: automation
ms.subservice: change-inventory-management
keywords: inventário, automatização,alteração, controlo
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 5142286bc50620d5a12a0722b3c4f9b8b75f5b73
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745271"
---
# <a name="manage-inventory-collection-from-vms"></a>Gerir a recolha de inventário a partir de VMs

Pode ativar o rastreio de inventário de um VM Azure a partir da página de recursos da máquina. Pode recolher e visualizar as seguintes informações de inventário nos seus computadores:

- Atualizações do Windows, aplicações do Windows, serviços, ficheiros e chaves de registo
- Pacotes de software Linux, daemons e ficheiros

O Rastreio e Inventário de Alterações de Automação Azure fornece uma interface de utilizador baseada no navegador para configurar e configurar a recolha de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/)

Este artigo assume que tem um VM para ativar com o Change Tracking e O Inventário. Se não tiver um VM Azure, pode [criar um VM](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Ativar a recolha de inventário a partir da página de recursos VM

1. No painel esquerdo do portal do Azure, selecione **Máquinas virtuais**.
2. Na lista de VMs, selecione uma máquina.
3. No menu **Recurso,** em **Operações,** selecione **Inventário**.
4. Selecione um espaço de trabalho de Log Analytics para armazenar os seus registos de dados.
    Se não existir uma área de trabalho disponível para essa região, é-lhe pedido para criar uma área de trabalho predefinida e uma conta de automatização.
5. Para começar a ativar o computador, selecione **Ativar**.

   ![Ver opções de inclusão](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Uma barra de estado notifica que a funcionalidade de Rastreio e Inventário de Alterações está a ser ativada. Este processo pode demorar até 15 minutos a concluir. Durante este tempo, pode fechar a janela, ou pode mantê-la aberta e anota quando a funcionalidade está ativada. Pode monitorizar o estado da implementação a partir do painel de notificações.

   ![Ver o inventário](./media/automation-vm-inventory/inventory-onboarded.png)

Quando a implementação estiver concluída, a barra de estado desaparece. O sistema ainda está a recolher dados de inventário e os dados podem não estar visíveis. Um conjunto completo de dados pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configurar as definições de inventário

Por predefinição, o software, os serviços do Windows e os daemons Linux estão configurados para a recolha. Para recolher o registo do Windows e o inventário de ficheiros, configure as definições de recolha do inventário.

1. Na página de Inventário, clique em **Definições de Edição** no topo da página.
2. Para adicionar uma nova definição de recolha, vá para a categoria de definição que pretende adicionar selecionando o **windows registry,** **Windows Files**ou o separador **Ficheiros Linux.**
3. Selecione a categoria apropriada e clique em **Adicionar** no topo da página.

As seguintes secções fornecem informações sobre cada propriedade que podem ser configuradas para as várias categorias.

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
|Ativado     | É verdade se a definição for aplicada, e falsa de outra forma.        |
|Nome do Item     | O nome amigável do ficheiro a ser rastreado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.       |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Ficheiros Linux

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | É verdade se a definição for aplicada, e falsa de outra forma.        |
|Nome do Item     | O nome amigável do ficheiro a ser rastreado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **/etc/*.conf**.       |
|Tipo de Caminho     | O tipo de item a ser rastreado. Os valores são Arquivo e Diretório.        |
|Recursão     | É verdade que se a recursion é usada quando se procura o item a ser rastreado, e Falso de outra forma.        |
|Utilizar o Sudo     | É verdade que o sudo é usado ao verificar o item, e falso de outra forma.         |
|Ligações     | Valor indicando como as ligações simbólicas são tratadas ao atravessar diretórios. Os valores possíveis são: <br> Ignorar - ignora as ligações simbólicas e não inclui os ficheiros/diretórios referenciados<br>Seguir - segue as ligações simbólicas durante a recursão e também inclui os ficheiros/diretórios referenciados<br>Gerir - segue as ligações simbólicas e permite alterar o tratamento do conteúdo devolvido      |

## <a name="manage-machine-groups"></a>Gerir grupos de máquinas

O inventário permite-lhe criar e visualizar grupos de máquinas em registos do Monitor Azure. Os grupos de máquinas são coleções de máquinas definidas por uma consulta nos registos do Monitor Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para ver os grupos de máquinas, selecione o separador **grupos máquinas** na página Inventário.

![Ver grupos de máquinas na página de inventário](./media/automation-vm-inventory/inventory-machine-groups.png)

Selecionando um grupo de máquinas da lista abre a página dos grupos máquinas. Esta página mostra detalhes sobre o grupo de máquinas. Estes detalhes incluem a consulta de log analytics que é usada para definir o grupo. Na parte inferior da página, está uma lista páginada das máquinas que fazem parte desse grupo.

![Ver página de grupo de máquinas](./media/automation-vm-inventory/machine-group-page.png)

Clique **+ Clone** para clonar o grupo da máquina. Tens de dar ao grupo um novo nome e pseudónimo para o grupo. A definição pode ser alterada neste momento. Depois de alterar a consulta, clique em **Validar a consulta** para pré-visualizar as máquinas que seriam selecionadas. Quando estiver satisfeito com o grupo, clique em **Criar** o grupo de máquinas.

Se quiser criar um novo grupo de máquinas, clique + **Crie um grupo de máquinas**. Este botão abre a página criar uma máquina de **grupo,** onde pode definir o seu novo grupo. Clique em **Criar** para criar o grupo.

![Criar novo grupo de máquinas](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>Desligue o seu VM da gestão

Para remover o seu VM da gestão de inventário:

1. No painel esquerdo do portal Azure, selecione **Log Analytics**, e, em seguida, selecione o espaço de trabalho que utilizou ao ativar o seu VM para rastreio e inventário de alterações.
2. Na página Log Analytics, abra o menu **Recurso.**
3. Selecione **Máquinas Virtuais** sob **fontes**de dados do espaço de trabalho .
4. Na lista, selecione o VM que pretende desligar. A máquina tem uma marca de verificação verde ao lado **deste espaço** de trabalho na coluna **De Ligação OMS.**

   >[!NOTE]
   >A Suite de Gestão de Operações (OMS) é agora referida como registos do Monitor Azure.
   
5. No topo da página seguinte, clique em **Desligar**.
6. Na janela de confirmação, clique **em Sim** para desligar a máquina da gestão.

## <a name="next-steps"></a>Passos seguintes

* [Gerir o rastreio e o inventário de alterações](change-tracking-file-contents.md)
* [Acompanhe as mudanças de software no seu ambiente com Change Tracking](../log-analytics/log-analytics-change-tracking.md).
* [Gestão de Atualizações em Azure.](../operations-management-suite/oms-solution-update-management.md)