---
title: Interface do usuário do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve a interface do usuário do StorSimple Snapshot Manager e explica como usá-la para gerenciar trabalhos de backup e o catálogo de backup.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933980"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Usar a interface do usuário do StorSimple Snapshot Manager para gerenciar trabalhos de backup e catálogo de backup

## <a name="overview"></a>Visão geral
O Snapshot Manager StorSimple tem uma interface de usuário intuitiva que você pode usar para executar e gerenciar backups. Este tutorial fornece uma introdução à interface do usuário e, em seguida, explica como usar cada um dos componentes. Para obter uma descrição detalhada do Snapshot Manager StorSimple, consulte [o que é o snapshot Manager storsimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição do console
Para exibir a interface do usuário, clique no ícone StorSimple Snapshot Manager na área de trabalho. A janela do console é exibida, conforme mostrado na ilustração a seguir.

![Painéis de Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela do console tem cinco elementos principais. Clique no link apropriado para obter uma descrição completa de cada elemento.

* [Barra de menus](#menu-bar) 
* [Barra de ferramentas](#tool-bar) 
* [Painel escopo](#scope-pane) 
* [Painel de resultados](#results-pane) 
* [Painel Ações](#actions-pane) 

Além disso, o Snapshot Manager StorSimple dá suporte à [navegação por teclado e a vários atalhos](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Acessibilidade do console
A interface do usuário Snapshot Manager do StorSimple dá suporte aos recursos de acessibilidade fornecidos pelo sistema operacional Windows e pelo MMC (console de gerenciamento Microsoft), bem como alguns atalhos de teclado específicos ao StorSimple Snapshot Manager. 

* Para obter uma descrição dos recursos de acessibilidade do Windows, vá para [atalhos de teclado do Windows](https://support.microsoft.com/kb/126449). 
* Para obter uma descrição dos recursos de acessibilidade do MMC, acesse [acessibilidade para o MMC 3,0](https://technet.microsoft.com/library/cc766075.aspx)
* Para obter uma descrição dos recursos de acessibilidade do StorSimple Snapshot Manager, vá para [navegação de teclado e atalhos](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra de menus
A barra de menus na parte superior da janela do console contém os menus [arquivo](#file-menu), [ação](#action-menu), [Exibir](#view-menu), [favoritos](#favorites-menu), [janela](#window-menu)e [ajuda](#help-menu) .

Clique em qualquer item na barra de menus para ver uma lista de comandos disponíveis nesse menu. O exemplo a seguir mostra o menu **Exibir** selecionado na barra de menus.

![Menu Exibir selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Ficheiro
O menu **arquivo** contém comandos padrão do console de gerenciamento Microsoft (MMC).

#### <a name="menu-access"></a>Acesso ao menu
Para exibir o menu **arquivo** , clique em **arquivo** na barra de menus. O menu a seguir é exibido.

![Menu arquivo do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu **arquivo** .

| Item de menu | Descrição |
|:--- |:--- |
| Novo |Clique em **novo** para criar um novo console baseado no snapshot Manager StorSimple. |
| Aberto |Clique em **abrir** para abrir um console existente. |
| Guardar |Clique em **salvar** para salvar o console atual. |
| Guardar como |Clique em **salvar como** para criar uma nova instância renomeada do console atual. Use a opção **salvar como** para personalizar uma exibição e salvá-la para recuperação posterior. Por exemplo, você pode criar snap-ins de Snapshot Manager do StorSimple que apontam para servidores específicos. |
| Adicionar/remover snap-in |Clique em **Adicionar/remover snap-in** para adicionar ou remover snap-ins e organizar nós no painel **escopo** . Para obter mais informações, vá para [Adicionar, remover e organizar snap-ins e extensões no MMC 3,0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções |Clique em **Opções** para alterar o ícone do console, especificar modos de acesso do usuário e permissões ou excluir arquivos de console para aumentar o espaço disponível em disco. |
| Lista de caminhos de arquivo |Clique em um caminho na lista numerada para reabrir um arquivo que você abriu recentemente. |
| Sair |Clique em **sair** para fechar o menu **arquivo** . |

### <a name="action-menu"></a>Menu de ação
Use o menu **ação** para selecionar as ações disponíveis. Os itens disponíveis dependem da seleção feita no painel **escopo** ou no painel **resultados** .

#### <a name="menu-access"></a>Acesso ao menu
Para exibir o menu **ação** , siga um destes procedimentos:

* Clique com o botão direito do mouse em um item no painel **escopo** ou no painel **resultados** .
* Selecione um item no painel **escopo** ou no painel **resultados** e clique em **ação** na barra de menus. 

Por exemplo, se você selecionar o nó superior no painel **escopo** e clicar com o botão direito do mouse ou clicar em **ação** na barra de menus, o menu a seguir será exibido.

![Menu de ação do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O painel **ações** (à direita do console) contém a mesma lista de ações que o menu **ação** . Além disso, o painel **ações** contém as opções do menu **Exibir** , que permitem que você crie uma exibição personalizada do painel **resultados** .

![Painel Ações com o menu exibir aberto](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir contém uma lista alfabética de ações de Snapshot Manager do StorSimple. 

* A coluna **ação** lista ações que podem ser executadas em nós e resultados. 
* A coluna de **navegação** explica como exibir o menu de **ação** apropriado para que você possa selecionar a ação. Algumas ações aparecem em vários menus de **ação** . Para essas ações, selecione uma opção de **navegação** na lista com marcadores. 
* A coluna **Descrição** descreve como usar cada ação no menu **ação** ou no painel Ações e explica o que ele faz.

> [!NOTE]
> O painel **ações** e os menus **ação** contêm opções adicionais, como **Exibir**, **nova janela**daqui, **Atualizar**, **Exportar lista**e **ajuda**. Essas opções estão disponíveis como parte do MMC e não são específicas do StorSimple Snapshot Manager. A tabela inclui descrições dessas opções.
> 
> 

| Ação | Navegação | Descrição |
|:--- |:--- |:--- |
| Autenticar |Clique no nó **dispositivos** e clique com o botão direito do mouse em um dispositivo no painel de **resultados** . |Clique em **autenticar** para inserir a senha que você configurou para o dispositivo. |
| Clone |Expanda **Catálogo de backup**, expanda **instantâneos de nuvem**, clique em um backup com data e selecione um volume no painel de **resultados** . |Clique em **clonar** para criar uma cópia de um instantâneo de nuvem e armazená-lo em um local que você designar. |
| Configurar um dispositivo |Clique com o botão direito do mouse no nó **dispositivos** . |Clique em **configurar um dispositivo** para configurar um único dispositivo ou vários dispositivos para se conectar ao host do Windows. |
| Criar política de backup |Efetue uma das seguintes ações:<ul><li>Clique com o botão direito do mouse em **políticas de backup**.</li><li>Clique ou expanda **grupos de volumes**e clique com o botão direito do mouse em um grupo de volumes.</li><li>Clique ou expanda **Catálogo de backup**e clique com o botão direito do mouse em um grupo de volumes.</li></ul> |Clique em **criar política de backup** para configurar um backup agendado para um grupo de volumes. |
| Criar grupo de volumes |Efetue uma das seguintes ações:<ul><li>Clique no nó **volumes** e clique com o botão direito do mouse em um volume no painel de **resultados** .</li><li>Clique com o botão direito do mouse no nó **grupos de volumes** .</li></ul> |Clique em **Criar grupo** de volumes para atribuir volumes a um grupo de volumes. |
| Eliminar |Clique em um nó ou resultado (esse item aparece em muitos menus de **ação** e painéis de **ações** .) |Clique em **excluir** para excluir o nó ou o resultado que você selecionou. Quando a caixa de diálogo de confirmação for exibida, confirme ou cancele a exclusão. |
| Detalhes |Clique no nó **dispositivos** e clique com o botão direito do mouse em um dispositivo no painel de **resultados** . |Clique em **detalhes** para ver os detalhes de configuração de um dispositivo. |
| Editar |Clique em **políticas de backup**e, em seguida, clique com o botão direito do mouse em uma política no painel de **resultados** . |Clique em **Editar** para alterar o agendamento de backup de um grupo de volumes. |
| Exportar lista |Clique em qualquer nó ou resultado (este item aparece em todos os menus de **ação** e painéis de **ações** .) |Clique em **Exportar lista** para salvar uma lista em um arquivo de valores separados por vírgulas (CSV). Em seguida, você pode importar esse arquivo para um aplicativo de planilha para análise. |
| Ajuda |Clique em qualquer nó ou resultado. (Este item aparece em todos os menus de **ação** e painéis de **ações** .) |Clique em **ajuda** para abrir a ajuda online em uma janela separada do navegador. |
| Nova Janela a Partir Daqui |Clique em qualquer nó ou resultado (este item aparece em todos os menus de **ação** e painéis de **ações** .) |Clique em **nova janela aqui** para abrir uma nova janela de snapshot Manager do StorSimple. |
| Atualizar |Clique em qualquer nó ou resultado (este item aparece em todos os menus de **ação** e painéis de **ações** .) |Clique em **Atualizar** para atualizar a janela de snapshot Manager do StorSimple exibida no momento. |
| Atualizar dispositivo |Clique no nó **dispositivos** e clique com o botão direito do mouse em um dispositivo no painel de **resultados** . |Clique em **Atualizar dispositivo** para sincronizar um dispositivo conectado específico com o StorSimple snapshot Manager. |
| Atualizar dispositivos |Clique com o botão direito do mouse no nó **dispositivos** . |Clique em **Atualizar dispositivos** para sincronizar sua lista de dispositivos conectados com o StorSimple snapshot Manager. |
| Examinar volumes novamente |Clique com o botão direito do mouse no nó **volumes** . |Clique em **examinar volumes novamente** para atualizar a lista de volumes que aparece no painel de **resultados** . |
| Restauro |Expanda **Catálogo de backup**, expanda um grupo de volumes, expanda **instantâneos locais** ou **instantâneos de nuvem**e clique com o botão direito do mouse em um backup. |Clique em **restaurar** para substituir os dados do grupo de volume atual pelos dados do backup selecionado. |
| Fazer backup |Efetue uma das seguintes ações:<ul><li>Expanda **grupos de volumes**e clique com o botão direito do mouse em um grupo de volumes.</li><li>Expanda **Catálogo de backup**e clique com o botão direito do mouse em um grupo de volumes.</li></ul> |Clique em **fazer backup** para iniciar um trabalho de backup imediatamente. |
| Alternar exibição de importações |Clique com o botão direito do mouse no nó superior no painel **escopo** (o nó **snapshot Manager do StorSimple** nos exemplos). |Clique em **alternar exibição de importações** para mostrar ou ocultar os grupos de volumes e os backups associados que foram importados do painel do serviço StorSimple Device Manager. |

### <a name="view-menu"></a>Menu Ver
Use o menu **Exibir** para criar uma exibição personalizada do conteúdo do painel **resultados** . O menu **Exibir** contém opções **Adicionar/remover colunas** e **Personalizar** .

#### <a name="menu-access"></a>Acesso ao menu
Você pode acessar o menu **Exibir** na barra de menus ou no painel **ações** .

![Menu de exibição do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu **Exibir** .

| Item de menu | Descrição |
|:--- |:--- |
| Adicionar/remover colunas |Clique em **Adicionar/remover colunas** para adicionar ou remover colunas no painel de **resultados** . |
| Personalizar |Clique em **Personalizar** para mostrar ou ocultar itens na janela do console do StorSimple snapshot Manager. |

### <a name="favorites-menu"></a>Menu Favoritos
Use o menu **favoritos** para adicionar, remover e organizar exibições de página e tarefas que você usa com frequência. 

#### <a name="menu-access"></a>Acesso ao menu
Você pode acessar o menu **favoritos** na barra de menus.

![Menu Favoritos do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu **favoritos** .

| Item de menu | Descrição |
|:--- |:--- |
| Adicionar aos Favoritos |Clique em **Adicionar aos favoritos** para adicionar o modo de exibição atual à lista de favoritos. |
| Organizar favoritos |Clique em **organizar favoritos** para organizar o conteúdo da pasta favoritos. |

### <a name="window-menu"></a>Menu janela
Use o menu **janela** para adicionar e reorganizar as janelas do console do StorSimple snapshot Manager.

#### <a name="menu-access"></a>Acesso ao menu
Você pode acessar o menu **janela** na barra de menus.

![Menu de janela do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra as janelas que estão abertas no momento. Clique em qualquer janela nessa lista para colocar a janela em primeiro plano. 

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu janela.

| Item de menu | Descrição |
|:--- |:--- |
| Nova Janela |Clique em **nova janela** para abrir uma nova janela de console (além da janela existente). |
| Cascade |Clique em **cascata** para exibir as janelas abertas do console em um estilo em cascata. |
| Enquadrado horizontalmente |Clique no **bloco horizontalmente** para exibir as janelas abertas do console em um formato de bloco (ou grade). |
| Organizar ícones |Se você tiver várias janelas de console abertas e espalhadas pela área de trabalho, minimize-as e clique em **organizar ícones** para organizá-las em uma linha horizontal na parte inferior da tela. |

### <a name="help-menu"></a>Menu Ajuda
Use o menu **ajuda** para exibir a ajuda online disponível para o StorSimple snapshot Manager e o MMC. Você também pode exibir informações sobre as versões de software do MMC e do StorSimple Snapshot Manager que estão atualmente instaladas no seu sistema. 

Você pode acessar o menu **ajuda** na barra de menus. Você também pode acessar os tópicos da ajuda do StorSimple Snapshot Manager no painel **ações** .

![Menu de ajuda do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu ajuda.

| Item de menu | Descrição |
|:--- |:--- |
| Ajuda sobre o StorSimple Snapshot Manager |Clique em **ajuda no StorSimple snapshot Manager** para abrir a ajuda do storsimple snapshot Manager em uma janela separada. |
| Tópicos de ajuda |Clique em **Tópicos da ajuda** para abrir a ajuda online do MMC em uma janela separada. |
| Site do TechCenter |Clique em **site do TechCenter** para abrir o Microsoft TechNet Tech Center Home Page em uma janela separada. |
| Sobre o console de gerenciamento Microsoft |Clique em **sobre o console de gerenciamento Microsoft** para ver qual versão do console de gerenciamento Microsoft está instalada em seu sistema. |
| Sobre o StorSimple Snapshot Manager |Clique em **sobre o StorSimple snapshot Manager** para ver qual versão do snap-in está instalada no sistema. |

## <a name="tool-bar"></a>Barra de ferramentas
A barra de ferramentas, localizada abaixo da barra de menus, contém ícones de navegação e tarefa. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições de ícones
A tabela a seguir descreve os ícones que aparecem na barra de ferramentas. 

| Ícone | Descrição |
|:--- |:--- |
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Clique no ícone de seta para a esquerda para retornar à página anterior. |
| ![SETA PARA A DIREITA](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Clique na seta para a direita para ir para a próxima página (se a seta estiver cinza, a ação não estará disponível). |
| ![Ícone para cima](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Clique no ícone para cima para subir um nível na árvore de console (o painel **escopo** ). |
| ![Mostrar/ocultar árvore de console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Clique no ícone mostrar/ocultar árvore de console para mostrar ou ocultar o painel **escopo** . |
| ![Exportar lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Clique no ícone exportar lista para exportar uma lista para um arquivo CSV que você especificar. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Clique no ícone de ajuda para abrir um tópico de ajuda online do MMC. |
| ![Mostrar/ocultar painel Ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Clique no ícone mostrar/ocultar **ações** do painel para mostrar ou ocultar o painel **ações** . |

## <a name="scope-pane"></a>Painel escopo
O painel **escopo** é o painel mais à esquerda na interface do usuário do StorSimple snapshot Manager. Ele contém a árvore do console (ou nó) e é o mecanismo de navegação principal para o StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Estrutura do painel de escopo
O painel **escopo** contém uma série de objetos clicáveis (nós) organizados em uma estrutura de árvore. 

![Painel escopo](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Para expandir ou recolher um nó, clique no ícone de seta ao lado do nome do nó.
* Para exibir o status ou o conteúdo de um nó, clique no nome do nó. As informações são exibidas no painel **resultados** . 

O painel **escopo** contém os seguintes nós: 

* [Nó dispositivos](#devices-node) 
* [Nó volumes](#volumes-node) 
* [Nó grupos de volumes](#volume-groups-node) 
* [Nó políticas de backup](#backup-policies-node) 
* [Nó do catálogo de backup](#backup-catalog-node) 
* [Nó trabalhos](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas do painel escopo
Você pode usar o painel **escopo** para concluir uma ação em um nó específico. Para selecionar uma tarefa, siga um destes procedimentos:

* Clique com o botão direito do mouse no nó e selecione a tarefa no menu que aparece.
* Clique no nó e, em seguida, clique em **ação** na barra de menus. Selecione a tarefa no menu que aparece.
* Clique no nó e, em seguida, selecione a ação no painel **ações** .

Quando você seleciona um nó e usa qualquer um desses métodos para ver uma lista de tarefas, somente as ações que podem ser executadas nesse nó são mostradas.

### <a name="devices-node"></a>Nó dispositivos
O nó **dispositivos** representa os dispositivos storsimple e os dispositivos virtuais storsimple que estão conectados ao StorSimple snapshot Manager. Selecione este nó para se conectar e configurar um dispositivo e importar seus volumes associados, grupos de volumes e cópias de backup existentes. Vários dispositivos podem ser conectados a um único host.

* Para expandir o nó, clique no ícone de seta ao lado de **dispositivos**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **dispositivos** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de dispositivos configurados, clique em **dispositivos** no painel **escopo** . A lista de dispositivos, junto com informações sobre cada dispositivo, aparece no painel **resultados** .

### <a name="volumes-node"></a>Nó volumes
O nó **volumes** representa as unidades que correspondem aos volumes montados pelo host, incluindo aqueles descobertos por meio de iSCSI e aqueles descobertos por meio de um dispositivo. Use este nó para exibir a lista de volumes disponíveis e atribuir volumes individuais a grupos de volumes.

* Para expandir o nó, clique no ícone de seta ao lado de **volumes**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **volumes** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de volumes, clique em **volumes** no painel **escopo** . A lista de volumes, junto com informações sobre cada volume, aparece no painel **resultados** .

### <a name="volume-groups-node"></a>Nó grupos de volumes
Os grupos de volumes também são conhecidos como grupos de consistência. Cada grupo de volumes é um pool de volumes relacionados ao aplicativo que ajuda a garantir a consistência do aplicativo durante as operações de backup. Use o nó **grupos de volumes** para configurar esses grupos e para fazer backups interativos ou criar agendamentos de backup. 

* Para expandir o nó, clique no ícone de seta ao lado de **grupos de volumes**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **grupos de volumes** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de grupos de volumes, clique em **grupos de volumes** no painel **escopo** . A lista de grupos de volumes, juntamente com informações sobre cada grupo de volumes, aparece no painel **resultados** .

### <a name="backup-policies-node"></a>Nó políticas de backup
As políticas de backup são agendas de trabalho para instantâneos locais e na nuvem. Use o nó **políticas de backup** para especificar com que frequência um backup é criado e por quanto tempo um backup deve ser retido. 

* Para expandir o nó, clique no ícone de seta ao lado de **políticas de backup**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **políticas de backup** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de políticas de backup, clique em **políticas de backup** no painel **escopo** . A lista de políticas de backup, junto com informações sobre cada política, aparece no painel **resultados** .

> [!NOTE]
> Você pode manter no máximo 64 backups.


### <a name="backup-catalog-node"></a>Nó do catálogo de backup
O nó **Catálogo de backup** contém listas de backups no local e fora do local dos volumes do Azure StorSimple. Esse nó é organizado por grupo de volumes, e cada contêiner de grupo de volumes contém estruturas separadas para instantâneos locais (o nó do **instantâneo local**) e instantâneos de nuvem (o nó **instantâneos de nuvem** ). Quando expandida, cada contêiner de grupo de volumes lista todos os backups bem-sucedidos que foram realizados interativamente ou por uma política configurada.

* Para expandir o nó, clique no ícone de seta ao lado de **Catálogo de backup**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **Catálogo de backup** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de instantâneos de backup, clique em **Catálogo de backup** no painel **escopo** . A lista de instantâneos, junto com informações sobre cada instantâneo, aparece no painel **resultados** .

### <a name="local-snapshots-node"></a>Nó de instantâneos locais
O nó **instantâneos locais** lista os instantâneos locais para um grupo de volumes específico. O nó está localizado no nó **Catálogo de backup** no painel **escopo** . Os instantâneos locais são cópias point-in-time de dados de volume que são armazenados no dispositivo Azure StorSimple. Normalmente, esse tipo de backup pode ser criado e restaurado rapidamente. Você pode usar um instantâneo local como faria com uma cópia de backup local.

* Para expandir o nó, clique no ícone de seta ao lado de **instantâneos locais**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **instantâneos locais** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de instantâneos locais, clique em **instantâneos locais** no painel **escopo** . A lista de instantâneos, junto com informações sobre cada instantâneo, aparece no painel **resultados** .

### <a name="cloud-snapshots-node"></a>Nó de instantâneos de nuvem
O nó **instantâneos de nuvem** lista os instantâneos de nuvem para um grupo de volumes específico. O nó está localizado no nó **Catálogo de backup** no painel **escopo** . Instantâneos de nuvem são cópias point-in-time de dados de volume que são armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo replicado em um sistema de armazenamento diferente, fora do local. Os instantâneos de nuvem são particularmente úteis em cenários de recuperação de desastres.

* Para expandir o nó, clique no ícone de seta ao lado de **instantâneos de nuvem**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **instantâneos de nuvem** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de instantâneos de nuvem, clique em **instantâneos de nuvem** no painel **escopo** . A lista de instantâneos, junto com informações sobre cada instantâneo, aparece no painel **resultados** .

### <a name="jobs-node"></a>Nó trabalhos
O nó **trabalhos** contém informações sobre os trabalhos de backup agendados, em execução e concluídos recentemente. 

* Para expandir o nó, clique no ícone de seta ao lado de **trabalhos**.
* Para ver um menu de ações disponíveis, clique com o botão direito do mouse no nó **trabalhos** ou clique com o botão direito do mouse em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de trabalhos agendados, expanda o nó **trabalhos** e clique em **agendado**. A lista de trabalhos configurados anteriormente e informações sobre cada trabalho aparece no painel **resultados** . 
* Para ver uma lista de trabalhos concluídos recentemente, expanda o nó **trabalhos** e clique em **últimas 24 horas**. Uma lista de trabalhos que foram concluídos nas últimas 24 horas aparece no painel **resultados** . O painel **resultados** também contém informações sobre cada trabalho concluído.
* Para ver uma lista de trabalhos em execução no momento, expanda o nó **trabalhos** e clique em **executando**. A lista de trabalhos em execução no momento e informações sobre cada trabalho aparece no painel **resultados** .

## <a name="results-pane"></a>Painel de resultados
O painel de **resultados** é o painel central na interface do usuário do StorSimple snapshot Manager. Ele contém listas e informações detalhadas de status para o nó selecionado no painel **escopo** .

### <a name="example"></a>Exemplo
Para ver o exemplo a seguir, clique no nó **grupos de volumes** no painel **escopo** . O painel **resultados** exibe uma lista de grupos de volumes com detalhes sobre cada grupo.

![Painel de resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Você pode configurar os detalhes mostrados no painel de **resultados** : clique com o botão direito do mouse em um nó no painel **escopo** , clique em **Exibir**e, em seguida, clique em **Adicionar/remover colunas**.

## <a name="actions-pane"></a>painel Actions
O painel **ações** é o painel direito na interface do usuário do StorSimple snapshot Manager. Ele contém um menu de operações que você pode executar no nó, na exibição ou nos dados que você selecionar no painel **escopo** ou no painel **resultados** . O painel **ações** contém os mesmos comandos que os menus de **ação** disponíveis para os itens no painel **escopo** e no painel **resultados** . Para obter uma descrição de cada ação, consulte a tabela na seção do menu **ação** .

### <a name="examples"></a>Exemplos
Para ver o exemplo a seguir, no painel **escopo** , expanda o nó **trabalhos** e clique em **agendado**. O painel **ações** exibe as ações disponíveis para o nó **agendado** .

![Exemplo de trabalhos agendados do painel Ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, no painel **escopo** , expanda o nó **trabalhos** , clique em **agendado**e, em seguida, clique em um trabalho agendado no painel **resultados** . O painel **ações** exibe as ações disponíveis para o trabalho agendado, conforme mostrado no exemplo a seguir.

![Exemplo de ações de trabalho do painel Ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navegação por teclado e atalhos
O StorSimple Snapshot Manager habilita os recursos de acessibilidade do sistema operacional Windows e do MMC (console de gerenciamento Microsoft). Ele também inclui alguns recursos de navegação de teclado e atalhos específicos para o Snapshot Manager do StorSimple, conforme descrito nas seções a seguir.

* [Teclas de navegação do teclado](#keyboard-navigation-keys) 
* [Teclas de atalho da barra de menus](#menu-bar-shortcut-keys) 
* [Teclas de atalho do painel escopo](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Teclas de navegação do teclado
A tabela a seguir descreve as chaves que você pode usar para navegar pela interface do usuário do StorSimple Snapshot Manager. 

| Chave de navegação | Ação |
|:--- |:--- |
| Tecla de seta para baixo |Use a tecla de seta para baixo para mover verticalmente para o próximo item em um menu ou painel. |
| Enter |Pressione a tecla Enter para concluir uma ação e prossiga para a próxima etapa. Por exemplo, você pode pressionar Enter para selecionar **Avançar**, **OK**ou **criar**e ir para a próxima etapa em um assistente. |
| Esc |Pressione a tecla ESC para fechar um menu ou para cancelar e fechar uma página. |
| F1 |Pressione a tecla F1 para exibir um tópico da ajuda para a janela ativa no momento. |
| F5 |Pressione a tecla F5 para atualizar um nó. |
| F6 |Pressione a tecla F6 para mover do painel **escopo** para o painel **resultados** . |
| F10 |Pressione a tecla F10 para ir para a barra de menus. |
| Tecla Seta Para a Esquerda |Use a tecla de seta para a esquerda para mover horizontalmente de uma opção de barra de menus para a opção anterior. Quando você move para o item anterior na barra de menus, o menu de ação (ou contexto) para o item anterior é exibido. |
| Tecla Seta Para a Direita |Use a tecla de seta para a direita para mover horizontalmente de uma opção de barra de menus para a próxima. Quando você passa para o próximo item na barra de menus, o menu de ação (ou contexto) para o novo item é exibido. |
| Tecla Tab |Use a tecla Tab para mover para o próximo painel no console ou para a próxima seleção ou caixa de texto em uma página. |
| Tecla de seta para cima |Use a tecla de seta para cima para mover verticalmente para o item anterior em um menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Teclas de atalho da barra de menus
A tabela a seguir descreve as combinações de teclas de atalho para a barra de menus. Depois que você pressiona as teclas de atalho e o menu é aberto, você pode usar as teclas de atalho do menu (as chaves sublinhadas no menu). Para obter mais informações sobre a barra de menus, vá para [barra de menus](#menu-bar).

| Atalho | Resultado | Tecla de atalho do menu | Resultado |
|:--- |:--- |:--- |:--- |
| ALT + F |Abre o menu **arquivo** . |N |Abre uma nova instância de console. |
|  |O |Abre a página **Ferramentas administrativas** . | |
|  |S |Salva o console do Snapshot Manager do StorSimple. | |
|  |A |Abre a página **salvar como** . | |
|  |M |Abre a página **Adicionar/remover snap-in** . | |
|  |P |Abre a página **Opções** . | |
|  |H |Abre a ajuda online. | |
| ALT + A |Abre o menu **ação** . |I |Ativa e desativa a opção de exibição de importação. |
|  |W |Abre um novo console Snapshot Manager do StorSimple. | |
|  |F |Atualiza o console do Snapshot Manager do StorSimple. | |
|  |L |Abre a página **Exportar lista** . | |
|  |H |Abre a ajuda online. | |
| ALT + V |Abre o menu **Exibir** . |A |Abre a página **Adicionar/remover colunas** . |
|  |U |Abre a página **Personalizar modo de exibição** . | |
| ALT+O |Abre o menu **favoritos** . |A |Abre a página **Adicionar aos favoritos** . |
|  |O |Abre a página **organizar favoritos** . | |
| ALT + W |Abre o menu **janela** . |N |Abre outra janela de Snapshot Manager do StorSimple. |
|  |C |Exibe todas as janelas de console abertas em um estilo em cascata. | |
|  |T |Exibe todas as janelas de console abertas em um padrão de grade. | |
|  |I |Organiza os ícones em uma linha horizontal na parte inferior da tela. | |
| ALT + H |Abre o menu **ajuda** . |H |Abre a ajuda online. |
|  |T |Abre a página da Web do Microsoft TechNet Tech Center. | |
|  |A |Abre a página **sobre o console de gerenciamento Microsoft** . | |

### <a name="scope-pane-shortcut-keys"></a>Teclas de atalho do painel escopo
As tabelas a seguir mostram as combinações de teclas de atalho para cada nó no painel **escopo** . 

* [Teclas de atalho do nó dispositivos](#devices-node-shortcut-keys)
* [Teclas de atalho do nó volumes](#volumes-node-shortcut-keys)
* [Teclas de atalho do nó grupos de volumes](#volume-groups-node-shortcut-keys)
* [Teclas de atalho do nó políticas de backup](#backup-policies-node-shortcut-keys)
* [Teclas de atalho do nó do catálogo de backup](#backup-catalog-node-shortcut-keys)
* [Teclas de atalho do nó trabalhos](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Teclas de atalho do nó dispositivos
| Atalho do menu | Resultado |
|:--- |:--- |
| C |Abre a página **configurar um dispositivo** . |
| D |Atualiza a lista de dispositivos e os detalhes do dispositivo. |
| V |Abre o menu **Exibir** . |
| W |Abre um novo console Snapshot Manager do StorSimple voltado para o nó **detalhes** . |
| F |Atualiza o console do Snapshot Manager do StorSimple. |
| L |Abre a página **Exportar lista** . |
| H |Abre a ajuda online. |

#### <a name="volumes-node-shortcut-keys"></a>Teclas de atalho do nó volumes
| Atalho do menu | Resultado |
|:--- |:--- |
| V |Atualiza a lista de volumes. |
| V (pressione duas vezes) |Abre o menu **Exibir** . |
| W |Abre um novo console Snapshot Manager do StorSimple focalizado no nó **volumes** . |
| F |Atualiza o console do Snapshot Manager do StorSimple. |
| L |Abre a página **Exportar lista** . |
| H |Abre a ajuda online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Teclas de atalho do nó grupos de volumes
| Atalho do menu | Resultado |
|:--- |:--- |
| G |Abre a página **criar um grupo de volumes** . |
| V |Abre o menu **Exibir** . |
| W |Abre um novo console Snapshot Manager do StorSimple voltado para o nó **grupos de volumes** . |
| F |Atualiza o console do Snapshot Manager do StorSimple. |
| L |Abre a página **Exportar lista** . |
| H |Abre a ajuda online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Teclas de atalho do nó políticas de backup
| Atalho do menu | Resultado |
|:--- |:--- |
| B |Abre a página **criar uma política** . |
| V |Abre o menu **Exibir** . |
| W |Abre um novo console Snapshot Manager do StorSimple voltado para o nó **grupos de volumes** . |
| F |Atualiza o console do Snapshot Manager do StorSimple. |
| L |Abre a página * * Exportar lista * *. |
| H |Abre a ajuda online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Teclas de atalho do nó do catálogo de backup
| Atalho do menu | Resultado |
|:--- |:--- |
| W |Abre um novo console Snapshot Manager do StorSimple voltado para o nó **grupos de volumes** . |
| F |Atualiza o console do Snapshot Manager do StorSimple. |
| H |Abre a ajuda online. |

#### <a name="jobs-node-shortcut-keys"></a>Teclas de atalho do nó trabalhos
| Atalho do menu | Resultado |
|:--- |:--- |
| V |Abre o menu **Exibir** . |
| W |Abre um novo console Snapshot Manager do StorSimple com foco no nó **trabalhos** . |
| F |Atualiza o console do Snapshot Manager do StorSimple. |
| L |Abre a página **Exportar lista** . |
| H |Abre a ajuda online |

## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o snapshot Manager StorSimple para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o snapshot Manager do StorSimple para conectar e gerenciar dispositivos](storsimple-snapshot-manager-manage-devices.md).

