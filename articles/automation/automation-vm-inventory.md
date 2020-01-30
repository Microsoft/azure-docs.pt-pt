---
title: Gerir uma máquina virtual do Azure com a recolha de inventário | Microsoft Docs
description: Gerir uma máquina virtual com a recolha de inventário
services: automation
ms.subservice: change-inventory-management
keywords: inventário, automatização,alteração, controlo
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d0324038b8a38d7eba84e5472b8f90439b0322c1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844824"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerir uma máquina virtual do Azure com a recolha de inventário

Pode ativar o controlo de inventário para uma máquina virtual do Azure a partir da página de recursos da máquina virtual. Pode recolher e visualizar as seguintes informações de inventário nos seus computadores:

- Software Windows (aplicações do Windows e atualizações do Windows), serviços, ficheiros e chaves de registo
- Daemons e ficheiros de software Linux (pacotes)

Este método fornece uma interface de utilizador baseada no browser para definir e configurar a recolha de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Este artigo pressupõe que você tenha uma VM para configurar a solução no. Se não tiver uma máquina virtual do Azure, [crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Ativar a recolha de inventário a partir da página de recursos da máquina virtual

1. No painel esquerdo do portal do Azure, selecione **Máquinas virtuais**.
2. Na lista de máquinas virtuais, selecione uma máquina virtual.
3. No menu de **recursos** , em **operações**, selecione **inventário**.
4. Selecione um espaço de trabalho Log Analytics para armazenar seus logs de dados.
    Se não existir uma área de trabalho disponível para essa região, é-lhe pedido para criar uma área de trabalho predefinida e uma conta de automatização.
5. Para iniciar a inclusão do seu computador, selecione **Ativar**.

   ![Ver opções de inclusão](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Uma barra de estado notifica-o de que a solução está a ser ativada. Este processo pode demorar até 15 minutos a concluir. Durante esse tempo, você pode fechar a janela ou pode mantê-la aberta e notificá-lo quando a solução estiver habilitada. Pode monitorizar o estado da implementação a partir do painel de notificações.

   ![Ver a solução de inventário imediatamente após a inclusão](./media/automation-vm-inventory/inventory-onboarded.png)

Quando a implementação estiver concluída, a barra de estado desaparece. O sistema ainda está a recolher dados de inventário e os dados podem não estar visíveis. Um conjunto completo de dados pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configurar as definições de inventário

Por predefinição, o software, os serviços do Windows e os daemons Linux estão configurados para a recolha. Para recolher o registo do Windows e o inventário de ficheiros, configure as definições de recolha do inventário.

1. Na exibição de **inventário** , selecione o botão **Editar configurações** na parte superior da janela.
2. Para adicionar uma nova definição de recolha, navegue para a categoria de definição que quer adicionar através dos separadores **Registo do Windows**, **Ficheiros do Windows** e **Ficheiros do Linux**.
3. Selecione a categoria apropriada e clique em **Adicionar** na parte superior da janela.

As tabelas a seguir fornecem informações sobre cada propriedade que pode ser configurada para as várias categorias.

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

### <a name="linux-files"></a>Arquivos do Linux

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

## <a name="manage-machine-groups"></a>Gerenciar grupos de computadores

O inventário permite criar e exibir grupos de computadores em logs de Azure Monitor. Grupos de computadores são coleções de computadores definidos por uma consulta em logs de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para exibir os grupos de computadores, selecione a guia **grupos de computadores** na página inventário.

![Exibir grupos de computadores na página de inventário](./media/automation-vm-inventory/inventory-machine-groups.png)

A seleção de um grupo de computadores na lista abre a página grupos de computadores. Esta página mostra detalhes sobre o grupo de computadores. Esses detalhes incluem a consulta do log Analytics que é usada para definir o grupo. Na parte inferior da página, é uma lista paginável de computadores que fazem parte desse grupo.

![Página exibir grupo de computadores](./media/automation-vm-inventory/machine-group-page.png)

Clique no botão **+ clone** para clonar o grupo de computadores. Aqui você deve dar ao grupo um novo nome e alias para o grupo. A definição pode ser alterada no momento. Depois de alterar a consulta, pressione **validar consulta** para visualizar os computadores que seriam selecionados. Quando estiver satisfeito com o grupo, clique em **criar** para criar o grupo de computadores

Se você quiser criar um novo grupo de computadores, selecione **+ criar um grupo de computadores**. Esse botão abre a **página criar um grupo de computadores** , na qual você pode definir o novo grupo. Clique em **Criar** para criar o grupo.

![Criar novo grupo de computadores](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Desligar a máquina virtual da gestão

Para remover a máquina virtual da gestão de inventário:

1. No painel esquerdo do portal do Azure, selecione **Log Analytics** e, em seguida, selecione a área de trabalho que utilizou quando efetuou a inclusão da máquina virtual.
2. Na janela **Log Analytics**, no menu **Recurso**, na categoria **Origens de Dados da Área de Trabalho**, selecione **Máquinas virtuais**.
3. Na lista, selecione a máquina virtual que quer desligar. A máquina virtual tem uma marca de verificação verde junto a **Esta área de trabalho** na coluna **Ligação OMS**.

   >[!NOTE]
   >O OMS agora é conhecido como logs de Azure Monitor.
   
4. Na parte superior da página seguinte, selecione **Desligar**.
5. Na janela de confirmação, selecione **Sim**.
    Esta ação desliga a máquina virtual da gestão.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a gestão de alterações nas definições de ficheiros e do registo nas suas máquinas virtuais, veja [Controlar as alterações de software com a solução Controlo de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre como gerenciar atualizações de pacote e do Windows em suas máquinas virtuais, consulte [a solução gerenciamento de atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).

