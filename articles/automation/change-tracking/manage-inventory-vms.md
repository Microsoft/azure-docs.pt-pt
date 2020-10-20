---
title: Gerir a coleção de inventário da Azure Automation a partir de VMs Microsoft Docs
description: Este artigo diz como gerir a recolha de inventário de VMs.
services: automation
ms.subservice: change-inventory-management
keywords: inventário, automatização,alteração, controlo
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209907"
---
# <a name="manage-inventory-collection-from-vms"></a>Gerir a recolha de inventário a partir de VMs

Pode ativar o rastreio de inventário para um Azure VM a partir da página de recursos da máquina. Pode recolher e visualizar as seguintes informações de inventário nos seus computadores:

- Atualizações do Windows, aplicações do Windows, serviços, ficheiros e chaves de registo
- Pacotes de software Linux, daemons e ficheiros

O Azure Automation Change Tracking and Inventory fornece uma interface de utilizador baseada no navegador para configurar e configurar a recolha de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Este artigo assume que tem um VM para ativar com Change Tracking e Inventário. Se não tiver um VM Azure, pode [criar um VM](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Ativar a recolha de inventário a partir da página de recursos VM

1. No painel esquerdo do portal do Azure, selecione **Máquinas virtuais**.
2. Na lista de VMs, selecione uma máquina.
3. No menu **Recursos,** em **Operações,** selecione **Inventário.**
4. Selecione um espaço de trabalho Log Analytics para armazenar os seus registos de dados.
    Se não existir uma área de trabalho disponível para essa região, é-lhe pedido para criar uma área de trabalho predefinida e uma conta de automatização.
5. Para começar a ativar o computador, selecione **Ative**.

   ![Ver opções de inclusão](./media/manage-inventory-vms/inventory-onboarding-options.png)

    Uma barra de estado notifica-o de que a funcionalidade de Rastreio e Inventário de Alterações está a ser ativada. Este processo pode demorar cerca de 15 minutos. Durante este tempo, pode fechar a janela, ou pode mantê-la aberta e notifica-o quando a funcionalidade está ativada. Pode monitorizar o estado da implementação a partir do painel de notificações.

   ![Ver inventário](./media/manage-inventory-vms/inventory-onboarded.png)

Quando a implementação estiver concluída, a barra de estado desaparece. O sistema ainda está a recolher dados de inventário e os dados podem não estar visíveis. Um conjunto completo de dados pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configurar as definições de inventário

Por predefinição, o software, os serviços do Windows e os daemons Linux estão configurados para a recolha. Para recolher o registo do Windows e o inventário de ficheiros, configure as definições de recolha do inventário.

1. Na página 'Inventário', clique em **Editar Definições** no topo da página.
2. Para adicionar uma nova definição de recolha, aceda à categoria de definição que pretende adicionar selecionando o **separador Registo**do Windows, **Windows Files**ou **Ficheiros Linux.**
3. Selecione a categoria adequada e clique em **Adicionar** no topo da página.

As seguintes secções fornecem informações sobre cada imóvel que podem ser configurados para as várias categorias.

### <a name="windows-registry"></a>Registo do Windows

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Group     | Um nome de grupo para agrupar ficheiros logicamente        |
|Chave do Registo do Windows   | O caminho para verificar o ficheiro, por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Ficheiros do Windows

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Verdade se a definição for aplicada, e falso de outra forma.        |
|Nome do Item     | O nome amigável do ficheiro a ser rastreado.        |
|Group     | Um nome de grupo para agrupar logicamente ficheiros.       |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Ficheiros Linux

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Verdade se a definição for aplicada, e falso de outra forma.        |
|Nome do Item     | O nome amigável do ficheiro a ser rastreado.        |
|Group     | Um nome de grupo para agrupar logicamente ficheiros.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **/etc/*.conf**.       |
|Tipo de Caminho     | O tipo de item a ser rastreado. Os valores são Arquivo e Diretório.        |
|Recursão     | É verdade que se a recursão for usada quando se procura que o item seja rastreado, e falso de outra forma.        |
|Use sudo     | É verdade se o sudo for usado ao verificar o artigo, e falso de outra forma.         |
|Ligações     | Valor indicando como as ligações simbólicas são tratadas ao atravessar diretórios. Os valores possíveis são: <br> Ignorar - ignora as ligações simbólicas e não inclui os ficheiros/diretórios referenciados<br>Seguir - segue as ligações simbólicas durante a recursão e também inclui os ficheiros/diretórios referenciados<br>Gerir - segue as ligações simbólicas e permite alterar o tratamento do conteúdo devolvido      |

## <a name="manage-machine-groups"></a>Gerir grupos de máquinas

O inventário permite-lhe criar e visualizar grupos de máquinas em registos do Azure Monitor. Os grupos de máquinas são coleções de máquinas definidas por uma consulta nos registos do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para visualizar os grupos de máquinas, selecione o separador **Grupos máquinas** na página 'Inventário'.

![Ver grupos de máquinas na página de inventário](./media/manage-inventory-vms/inventory-machine-groups.png)

Selecionar um grupo de máquinas da lista abre a página grupos Machine. Esta página mostra detalhes sobre o grupo de máquinas. Estes detalhes incluem a consulta de registo do Azure Monitor que é usada para definir o grupo. Na parte inferior da página, está uma lista paged das máquinas que fazem parte desse grupo.

![Ver página de grupo de máquinas](./media/manage-inventory-vms/machine-group-page.png)

Clique **+ Clone** para clonar o grupo de máquinas. Deve dar ao grupo um novo nome e pseudónimo para o grupo. A definição pode ser alterada neste momento. Depois de alterar a consulta, clique em **Validar consulta** para pré-visualizar as máquinas que seriam selecionadas. Quando estiver satisfeito com o grupo, clique em **Criar** para criar o grupo de máquinas.

Se pretender criar um novo grupo de máquinas, clique **em + Crie um grupo de máquinas**. Este botão abre a página **Criar um grupo de máquinas,** onde pode definir o seu novo grupo. Clique em **Criar** para criar o grupo.

![Criar novo grupo de máquinas](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>Desligue o seu VM da gestão

Para remover o seu VM da gestão de Change Tracking e Inventário:

1. No painel esquerdo do portal Azure, selecione **Log Analytics**e, em seguida, selecione o espaço de trabalho que utilizou ao ativar o seu VM para change tracking and inventory.
2. Na página **'Registar Analytics',** abra o menu **Recursos.**
3. Selecione **máquinas virtuais** em **fontes de dados do espaço de trabalho.**
4. Na lista, selecione o VM que pretende desligar. A máquina tem uma marca de verificação verde ao lado **deste espaço de trabalho** na coluna **OMS Connection.**

   >[!NOTE]
   >O Suíte de Gestão de Operações (OMS) é agora referido como registos do Monitor Azure.

5. No topo da página seguinte, clique **em Desligar**.
6. Na janela de confirmação, clique em **Sim** para desligar a máquina da gestão.

>[!NOTE]
>As máquinas ainda são mostradas depois de as ter desenrolado porque reportamos todas as máquinas inventariadas nas últimas 24 horas. Depois de desligar a máquina, é necessário esperar 24 horas antes de deixarem de estar listadas.

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre o trabalho com a funcionalidade, consulte [Manage Change Tracking and Inventory](manage-change-tracking.md).
* Para saber mais sobre as alterações de software de rastreio, consulte [as alterações de software track no seu ambiente com Change Tracking](overview.md).
* Para resolver problemas gerais com a funcionalidade, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](../troubleshoot/change-tracking.md).
