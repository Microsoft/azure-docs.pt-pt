---
title: Interface de utilizador StorSimple Snapshot Manager / Microsoft Docs
description: Descreve a interface de utilizador do StorSimple Snapshot Manager e explica como usá-la para gerir trabalhos de backup e o catálogo de backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: ead24b89dba889a4bb8f11d57eb687e7790aebfb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055916"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Utilize interface de utilizador StorSimple Snapshot Manager para gerir trabalhos de backup e catálogo de backup

## <a name="overview"></a>Descrição geral
O StorSimple Snapshot Manager tem uma interface de utilizador intuitiva que pode utilizar para fazer cópias de segurança. Este tutorial fornece uma introdução à interface do utilizador e, em seguida, explica como usar cada um dos componentes. Para uma descrição detalhada do StorSimple Snapshot Manager, consulte [o que é storSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição da consola
Para visualizar a interface do utilizador, clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. A janela da consola aparece, como mostra a seguinte ilustração.

![Painéis de gerente snapshot storSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela da consola tem cinco elementos principais. Clique no link apropriado para uma descrição completa de cada elemento.

* [Barra de menu](#menu-bar) 
* [Barra de ferramentas](#tool-bar) 
* [Painel de âmbito](#scope-pane) 
* [Painel de resultados](#results-pane) 
* [Painel de ações](#actions-pane) 

Além disso, o StorSimple Snapshot Manager suporta [a navegação de teclado e uma série de atalhos](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Acessibilidade à consola
A interface de utilizador StorSimple Snapshot Manager suporta as funcionalidades de acessibilidade fornecidas pelo sistema operativo Windows e pela Consola de Gestão da Microsoft (MMC), bem como alguns atalhos de teclado específicos do StorSimple Snapshot Manager. 

* Para uma descrição das funcionalidades de acessibilidade do Windows, aceda aos [atalhos do Teclado para Windows](https://support.microsoft.com/kb/126449). 
* Para uma descrição das funcionalidades de acessibilidade do MMC, aceda à [Acessibilidade para MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Para uma descrição das funcionalidades de acessibilidade do StorSimple Snapshot Manager, aceda à [navegação de teclado e atalhos](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra de menu
A barra de menus na parte superior da janela da consola contém [menus De ficheiro](#file-menu), [Ação](#action-menu), [Vista,](#view-menu) [Favoritos,](#favorites-menu) [Janela](#window-menu)e [Ajuda.](#help-menu)

Clique em qualquer item na barra de menu para ver uma lista de comandos disponíveis nesse menu. O exemplo a seguir mostra o menu **Ver** selecionado na barra de menus.

![Ver menu selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Ficheiro
O menu **Ficheiro** contém comandos padrão da Consola de Gestão da Microsoft (MMC).

#### <a name="menu-access"></a>Acesso ao menu
Para ver o menu **Ficheiro,** clique em **Arquivar** na barra de menus. O menu seguinte aparece.

![Menu de ficheiro storSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve itens que aparecem no menu **'Ficheiro'.**

| Item do menu | Descrição |
|:--- |:--- |
| Novo |Clique em **Novo** para criar uma nova consola com base no StorSimple Snapshot Manager. |
| Abrir |Clique **em Abrir** para abrir uma consola existente. |
| Guardar |Clique **em Guardar** para guardar a consola atual. |
| Guardar como |Clique **em Guardar Como** para criar uma nova instância renomeada da consola atual. Utilize a opção **Guardar como** personalizar uma vista e guardá-la para posterior recuperação. Por exemplo, pode criar snap-ins do StorSimple Snapshot Manager que apontam para servidores específicos. |
| Adicionar/remover snap-in |Clique **em Adicionar/Remover Snap-in** para adicionar ou remover snap-ins e para organizar nós no painel **De âmbito.** Para mais informações, aceda a [Adicionar, Remover e Organizar Snap-ins e Extensões em MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções |Clique **em Opções** para alterar o ícone da consola, especifique os modos e permissões de acesso ao utilizador ou elimine ficheiros de consola para aumentar o espaço disponível em disco. |
| Lista de caminhos de arquivo |Clique num caminho na lista numerada para reabrir um ficheiro que abriu recentemente. |
| Sair |Clique **em Sair** para fechar o menu **'Ficheiro'.** |

### <a name="action-menu"></a>Menu de ação
Utilize o menu **Ação** para selecionar as ações disponíveis. Os itens disponíveis dependem da seleção que faz no painel **de âmbito** ou **resultados.**

#### <a name="menu-access"></a>Acesso ao menu
Para ver o menu **Ação,** faça um dos seguintes:

* Clique com o botão direito num item no painel **de âmbito** ou no painel **de resultados.**
* Selecione um item no painel **de resultados** do Âmbito ou **resultados** e, em seguida, clique em **Ação** na barra de menu. 

Por exemplo, se selecionar o nó superior no painel **Scope** e, em seguida, clicar com o direito ou clicar em **Ação** na barra de menu, aparece o menu seguinte.

![Menu de ação do gestor de instantâneo StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O painel **de Ações** (à direita da consola) contém a mesma lista de ações que o menu **Ação.** Além disso, o painel **de Ações** contém as opções do menu **Ver,** que lhe permitem criar uma visão personalizada do painel **de resultados.**

![Painel de ações com menu de ver aberto](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir contém uma lista alfabética de ações do StorSimple Snapshot Manager. 

* A coluna **Action** lista as ações que pode realizar nos nós e resultados. 
* A coluna **Navegação** explica como exibir o menu **de ação** apropriado para que possa selecionar a ação. Algumas ações aparecem em vários menus **de Ação.** Para estas ações, selecione uma opção **de Navegação** da lista de balas. 
* A coluna **Descrição** descreve como utilizar cada ação no menu **ação** ou painel de Ações e explica o que faz.

> [!NOTE]
> Os menus **Ações** e **Ação** contêm opções adicionais, tais como **Ver,** **Nova Janela daqui,** **Refresh,** **Export List**e **Help**. Estas opções estão disponíveis como parte do MMC, e não são específicas do StorSimple Snapshot Manager. A tabela inclui descrições destas opções.
> 
> 

| Ação | Navegação | Descrição |
|:--- |:--- |:--- |
| Autenticar |Clique no nó **dispositivos** e clique com o botão direito num dispositivo no painel **de resultados.** |Clique em **Autenticar** para introduzir a palavra-passe que configura para o dispositivo. |
| Clone |Expandir **o Catálogo de Cópias de Segurança**, expandir cloud **snapshots,** clicar numa cópia de segurança datada e, em seguida, selecionar um volume no painel **de resultados.** |Clique em **Clone** para criar uma cópia de uma imagem em nuvem e armazene-a num local que designe. |
| Configurar um dispositivo |Clique com o botão direito no nó **dispositivos.** |Clique **em Configurar um Dispositivo** para configurar um único dispositivo ou vários dispositivos para ligar ao anfitrião do Windows. |
| Criar Política de Backup |Faça um dos seguintes:<ul><li>Políticas de backup de clique **à**direita .</li><li>Clique ou expanda **grupos de volume**e, em seguida, clique com o botão direito num grupo de volume.</li><li>Clique ou expanda **o Catálogo de Cópias de Segurança**e, em seguida, clique com o botão direito num grupo de volume.</li></ul> |Clique **em Criar A Política de Backup** para configurar uma cópia de segurança programada para um grupo de volume. |
| Criar Grupo de Volume |Faça um dos seguintes:<ul><li>Clique no nó **Volumes** e, em seguida, clique com o botão direito num volume no painel **de resultados.**</li><li>Clique com o botão direito no nó **Grupos de Volume.**</li></ul> |Clique **em Criar Grupo de Volume** para atribuir volumes a um grupo de volume. |
| Eliminar |Clique num nó ou resultado (Este item aparece em muitos menus **de Ação** e painéis **de ações.)** |Clique **em Eliminar** para eliminar o nó ou resultado que selecionou. Quando a caixa de diálogo de confirmação aparecer, confirme ou cancele a eliminação. |
| Detalhes |Clique no nó **dispositivos** e, em seguida, clique com o botão direito num dispositivo no painel **de resultados.** |Clique em **Detalhes** para ver os detalhes da configuração de um dispositivo. |
| Editar |Clique **em Políticas de Backup**e, em seguida, clique com o botão direito numa política no painel de **resultados.** |Clique **em Editar** para alterar o horário de backup de um grupo de volume. |
| Lista de Exportação |Clique em qualquer nó ou resultado (Este item aparece em todos os menus de **Ação** e painéis **de ações.)** |Clique na **Lista de Exportação** para guardar uma lista num ficheiro de valor separado por vírgula (CSV). Em seguida, pode importar este ficheiro para um pedido de cálculo para análise. |
| Ajuda |Clique em qualquer nó ou resultado. (Este item aparece em todos os menus **de Ação** e painéis **de ações.)** |Clique **em Ajuda** para abrir a ajuda on-line numa janela separada do navegador. |
| Nova Janela a Partir Daqui |Clique em qualquer nó ou resultado (Este item aparece em todos os menus de **Ação** e painéis **de ações.)** |Clique **em New Window from Here** para abrir uma nova janela StorSimple Snapshot Manager. |
| Atualizar |Clique em qualquer nó ou resultado (Este item aparece em todos os menus de **Ação** e painéis **de ações.)** |Clique em **Refresh** para atualizar a janela StorSimple Snapshot Manager atualmente apresentada. |
| Dispositivo de atualização |Clique no nó **dispositivos** e clique com o botão direito num dispositivo no painel **de resultados.** |Clique em **Refresh Device** para sincronizar um dispositivo conectado específico com o StorSimple Snapshot Manager. |
| Dispositivos de atualização |Clique com o botão direito no nó **dispositivos.** |Clique **em Dispositivos refresh** para sincronizar a sua lista de dispositivos conectados com o StorSimple Snapshot Manager. |
| Volumes de rescan |Clique com o botão direito no nó **Volumes.** |Clique em **volumes rescan** para atualizar a lista de volumes que aparecem no painel **de resultados.** |
| Restauro |Expanda **o Catálogo de Backup,** expanda um grupo de volume, expanda **as Imagens Locais** ou **Cloud Snapshots**e, em seguida, clique com o botão direito numa cópia de segurança. |Clique **em Restaurar** para substituir os dados do grupo de volume atual pelos dados da cópia de segurança selecionada. |
| Pegue o backup |Faça um dos seguintes:<ul><li>Expandir **grupos de volume**, e depois clicar com o botão direito de um grupo de volume.</li><li>Expanda **o Catálogo de Cópias de Segurança**e, em seguida, clique com o botão direito num grupo de volume.</li></ul> |Clique **em 'Fazer Backup'** para iniciar imediatamente um trabalho de reserva. |
| Toggle Imports Display |Clique com o nó de cima no painel **Scope** (o nó **do Gestor snapshot StorSimple** nos exemplos). |Clique **em Toggle Imports Display** para mostrar ou ocultar os grupos de volume e cópias de segurança associadas que foram importadas do painel de serviço StorSimple Device Manager. |

### <a name="view-menu"></a>Menu Ver
Utilize o menu **Ver** para criar uma visão personalizada do conteúdo do painel **de resultados.** O menu **Ver** contém **Colunas de Adicionar/Remover** e **Personalizar** opções.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder ao menu **Ver** na barra de menus ou no painel **Ações.**

![Menu StorSimple Snapshot Manager View](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve itens que aparecem no menu **Ver.**

| Item do menu | Descrição |
|:--- |:--- |
| Adicionar/remover colunas |Clique em **Adicionar/Remover Colunas** para adicionar ou remover colunas no painel **de resultados.** |
| Personalizar |Clique **em Personalizar** para mostrar ou ocultar itens na janela da consola StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu favorito
Utilize o menu **Favoritos** para adicionar, remover e organizar visualizações de página e tarefas que utiliza frequentemente. 

#### <a name="menu-access"></a>Acesso ao menu
Você pode aceder ao menu **Favoritos** na barra de menus.

![Menu favorito do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela que se segue descreve itens que aparecem no menu **Favoritos.**

| Item do menu | Descrição |
|:--- |:--- |
| Adicionar aos Favoritos |Clique **em Adicionar aos Favoritos** para adicionar a vista atual à sua lista de favoritos. |
| Organizar Favoritos |Clique **em Organizar Os Favoritos** para organizar o conteúdo da sua pasta Favoritos. |

### <a name="window-menu"></a>Menu de janela
Utilize o menu **Janela** para adicionar e reorganizar as janelas das consolas do Snapshot Manager StorSimple.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder ao menu **Da janela** na barra de menus.

![Menu de janela storSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra as janelas que estão abertas. Clique em qualquer janela dessa lista para trazer a janela para o primeiro plano. 

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve os itens que aparecem no menu 'Janela'.

| Item do menu | Descrição |
|:--- |:--- |
| Nova Janela |Clique em **Nova Janela** para abrir uma nova janela da consola (além da janela existente). |
| Cascata |Clique em **Cascata** para exibir as janelas das consolas abertas num estilo em cascata. |
| Azulejo horizontal |Clique **em Azulejo horizontalmente** para exibir as janelas da consola aberta num formato de azulejo (ou grelha). |
| Organize ícones |Se tiver várias janelas de consola abertas e espalhadas pelo seu ambiente de trabalho, minimize-as e clique em **Organizar Ícones** para as organizar numa linha horizontal na parte inferior do ecrã. |

### <a name="help-menu"></a>Menu Ajuda
Utilize o menu **Ajuda** para visualizar a ajuda online disponível para o StorSimple Snapshot Manager e o MMC. Também pode ver informações sobre as versões de software MMC e StorSimple Snapshot Manager que estão atualmente instaladas no seu sistema. 

Pode aceder ao menu **Ajuda** na barra de menus. Também pode aceder a StorSimple Snapshot Manager para ajudar tópicos a partir do painel **de Ações.**

![Menu de ajuda do gestor de snapshot storSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve itens que aparecem no menu Ajuda.

| Item do menu | Descrição |
|:--- |:--- |
| Ajuda no StorSimple Snapshot Manager |Clique **em Ajuda no StorSimple Snapshot Manager** para abrir a ajuda do StorSimple Snapshot Manager numa janela separada. |
| Tópicos de Ajuda |Clique em **Tópicos de Ajuda** para abrir a ajuda on-line do MMC numa janela separada. |
| TechCenter Web Site |Clique no **Web Site do TechCenter** para abrir a página inicial do Microsoft TechNet Tech Center numa janela separada. |
| Sobre a Consola de Gestão da Microsoft |Clique **em 'On Microsoft Management Console'** para ver qual a versão da Consola de Gestão da Microsoft instalada no seu sistema. |
| Sobre o StorSimple Snapshot Manager |Clique **em 'About StorSimple Snapshot Manager'** para ver qual a versão do snap-in instalada no seu sistema. |

## <a name="tool-bar"></a>Barra de ferramentas
A barra de ferramentas, localizada abaixo da barra de menu, contém ícones de navegação e de tarefa. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições do ícone
A tabela seguinte descreve os ícones que aparecem na barra de ferramentas. 

| Ícone | Descrição |
|:--- |:--- |
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Clique no ícone da seta esquerda para voltar à página anterior. |
| ![Seta para a direita](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Clique na seta direita para ir para a página seguinte (se a seta estiver cinzenta, a ação não está disponível). |
| ![Até o ícone](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Clique no ícone de cima para subir um nível na árvore da consola (o **painel Scope).** |
| ![Mostrar/esconder árvore de consola](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Clique no ícone da árvore da consola show/hide para mostrar ou esconder o painel **De âmbito.** |
| ![Lista de exportação](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Clique no ícone da lista de exportação para exportar uma lista para um ficheiro CSV que especifique. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Clique no ícone de ajuda para abrir um tópico de ajuda MMC on-line. |
| ![Painel de ações de exibição/ocultação](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Clique no ícone de painel de ações de **exibição/ocultação** para mostrar ou ocultar o painel **de Ações.** |

## <a name="scope-pane"></a>Painel de âmbito
O **painel Scope** é o painel mais à esquerda na UI StorSimple Snapshot Manager. Contém a árvore da consola (ou nó) e é o mecanismo de navegação primário para o StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Estrutura do painel de âmbito
O **painel Scope** contém uma série de objetos clicáveis (nós) organizados numa estrutura de árvores. 

![Painel de âmbito](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Para expandir ou colapsar um nó, clique no ícone da seta ao lado do nome do nó.
* Para ver o estado ou o conteúdo de um nó, clique no nome do nó. A informação aparece no painel **de resultados.** 

O **painel Scope** contém os seguintes nóns: 

* [Nó de dispositivos](#devices-node) 
* [Nó de volumes](#volumes-node) 
* [Nó de grupos de volume](#volume-groups-node) 
* [Nó de políticas de backup](#backup-policies-node) 
* [Nó de catálogo de backup](#backup-catalog-node) 
* [Nó de empregos](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas de painel de âmbito
Pode utilizar o painel **Scope** para completar uma ação num nó específico. Para selecionar uma tarefa, faça uma das seguintes:

* Clique no nó à direita e, em seguida, selecione a tarefa no menu que aparece.
* Clique no nó e, em seguida, clique em **Ação** na barra de menu. Selecione a tarefa no menu que aparece.
* Clique no nó e, em seguida, selecione a ação no painel **de Ações.**

Quando seleciona um nó e utiliza qualquer um destes métodos para ver uma lista de tarefas, apenas são apresentadas as ações que podem ser executadas nesse nó.

### <a name="devices-node"></a>Nó de dispositivos
O nó **de Dispositivos** representa os dispositivos StorSimple e os dispositivos virtuais StorSimple que estão ligados ao StorSimple Snapshot Manager. Selecione este nó para ligar e configurar um dispositivo e importe os seus volumes associados, grupos de volumes e cópias de backup existentes. Vários dispositivos podem ser ligados a um único hospedeiro.

* Para expandir o nó, clique no ícone da seta ao lado **dos Dispositivos**.
* Para ver um menu de ações disponíveis, clique no nó **dispositivos** ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de dispositivos configurados, clique em **Dispositivos** no **painel Scope.** A lista de dispositivos, juntamente com informações sobre cada dispositivo, aparece no painel **de resultados.**

### <a name="volumes-node"></a>Nó de volumes
O nó **volumes** representa as unidades que correspondem aos volumes montados pelo hospedeiro, incluindo os descobertos através do iSCSI e os descobertos através de um dispositivo. Utilize este nó para visualizar a lista de volumes disponíveis e atribuir volumes individuais a grupos de volume.

* Para expandir o nó, clique no ícone da seta ao lado **de Volumes**.
* Para ver um menu de ações disponíveis, clique no nó **volumes** ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de volumes, clique em **Volumes** no painel **Scope.** A lista de volumes, juntamente com informações sobre cada volume, aparece no painel **de resultados.**

### <a name="volume-groups-node"></a>Nó de grupos de volume
Os grupos de volume também são conhecidos como grupos de consistência. Cada grupo de volume é um conjunto de volumes relacionados com aplicações que ajudam a garantir a consistência da aplicação durante as operações de backup. Utilize o nó **Grupos de Volume** para configurar estes grupos e para fazer cópias de segurança interativas ou criar horários de backup. 

* Para expandir o nó, clique no ícone da seta ao lado **dos Grupos de Volume**.
* Para ver um menu de ações disponíveis, clique no nó **grupos** de volume ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de grupos de volume, clique em **Grupos de Volume** no painel **Scope.** A lista de grupos de volume, juntamente com informações sobre cada grupo de volume, aparece no painel **de resultados.**

### <a name="backup-policies-node"></a>Nó de políticas de backup
As políticas de backup são horários de trabalho para instantâneos locais e em nuvem. Utilize o nó **'Backup Policies'** para especificar a frequência com que é criada uma cópia de segurança e quanto tempo deve ser mantida uma cópia de segurança. 

* Para expandir o nó, clique no ícone de seta ao lado **das Políticas de Backup**.
* Para ver um menu de ações disponíveis, clique com o direito no nó **de Políticas de Backup** ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de políticas de backup, clique em **Políticas de Backup** no painel **Scope.** A lista de políticas de backup, juntamente com informações sobre cada política, aparece no painel **de resultados.**

> [!NOTE]
> Pode reter um máximo de 64 reforços.


### <a name="backup-catalog-node"></a>Nó de catálogo de backup
O nó **do Catálogo de Cópias de Segurança** contém listas de cópias de segurança no local e fora do local de volumes Azure StorSimple. Este nó é organizado por grupo de volume, e cada recipiente de grupo de volume contém estruturas separadas para instantâneos locais (o nó **snapshot local)** e instantâneos em nuvem (o nó **Cloud Snapshots).** Quando expandido, cada recipiente de grupo de volume lista todas as cópias de segurança bem sucedidas que foram tomadas interativamente ou por uma política configurada.

* Para expandir o nó, clique no ícone de seta ao lado **do Catálogo de Backup**.
* Para ver um menu de ações disponíveis, clique com o botão certo no nó do **Catálogo de Cópia de Segurança** ou clique no botão direito de qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de imagens de backup, clique no **Catálogo de Cópias de Segurança** no painel **Scope.** A lista de instantâneos, juntamente com informações sobre cada instantâneo, aparece no painel **de resultados.**

### <a name="local-snapshots-node"></a>Nó de snapshots locais
O nó **Snapshots Locais** lista instantâneos locais para um grupo de volume específico. O nó está localizado sob o nó **do catálogo de backup** no painel **Scope.** As imagens locais são cópias pontuais dos dados de volume que são armazenados no dispositivo Azure StorSimple. Normalmente, este tipo de backup pode ser criado e restaurado rapidamente. Pode usar uma foto local como uma cópia de reserva local.

* Para expandir o nó, clique no ícone da seta ao lado **do Snapshot local**.
* Para ver um menu de ações disponíveis, clique no nó **Snapshots local** ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de instantâneos locais, clique em **Snapshots Locais** no **painel Scope.** A lista de instantâneos, juntamente com informações sobre cada instantâneo, aparece no painel **de resultados.**

### <a name="cloud-snapshots-node"></a>Nó de Snapshots de Nuvem
O nó **Cloud Snapshots** lista instantâneos em nuvem para um grupo de volume específico. O nó está localizado sob o nó **do catálogo de backup** no painel **Scope.** As imagens em nuvem são cópias pontuais dos dados de volume que são armazenados na nuvem. Um instantâneo em nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferente e fora do local. As imagens em nuvem são particularmente úteis em cenários de recuperação de desastres.

* Para expandir o nó, clique no ícone da seta ao lado **do Cloud Snapshots**.
* Para ver um menu de ações disponíveis, clique no nó **Cloud Snapshots** ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de instantâneos em nuvem, clique em **Cloud Snapshots** no **painel Scope.** A lista de instantâneos, juntamente com informações sobre cada instantâneo, aparece no painel **de resultados.**

### <a name="jobs-node"></a>Nó de empregos
O nó **jobs** contém informações sobre trabalhos de reserva programados, em execução e recentemente concluídos. 

* Para expandir o nó, clique no ícone da seta ao lado **de Jobs**.
* Para ver um menu de ações disponíveis, clique com o nó **Jobs** ou clique com o botão direito em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de trabalhos programados, expanda o nó **Jobs** e, em seguida, clique em **Agendamento**. A lista de empregos previamente configurados e informações sobre cada trabalho aparece no painel **de resultados.** 
* Para ver uma lista de trabalhos recentemente concluídos, expanda o nó **Jobs** e, em seguida, clique **em Últimas 24 Horas**. Uma lista de trabalhos que foram concluídos nas últimas 24 horas aparece no painel **de resultados.** O **painel de resultados** também contém informações sobre cada trabalho concluído.
* Para ver uma lista de empregos que estão atualmente em execução, expanda o nó **Jobs** e, em seguida, clique em **Running**. A lista de empregos e informações atualmente em execução sobre cada trabalho aparece no painel **de resultados.**

## <a name="results-pane"></a>Painel de resultados
O painel de **resultados** é o painel central na UI StorSimple Snapshot Manager. Contém listas e informações detalhadas sobre o estado do nó selecionado no painel **Scope.**

### <a name="example"></a>Exemplo
Para ver o exemplo a seguir, clique no nó **Grupos de Volume** no painel **Scope.** O **painel de resultados** apresenta uma lista de grupos de volume com detalhes sobre cada grupo.

![Painel de resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Pode configurar os detalhes apresentados no painel **de resultados:** clique à direita num nó no painel **Scope,** clique em **Ver**e, em seguida, clique em **Adicionar/Remover Colunas**.

## <a name="actions-pane"></a>Painel de ações
O painel **de ações** é o painel certo na UI StorSimple Snapshot Manager. Contém um menu de operações que pode realizar no nó, visualização ou dados que seleciona no painel **de âmbito** ou **resultados.** O painel **de Ações** contém os mesmos comandos que os menus **Ação** que estão disponíveis para itens no painel **de** âmbito e **resultados.** Para uma descrição de cada ação, consulte a tabela na secção menu **Ação.**

### <a name="examples"></a>Exemplos
Para ver o seguinte exemplo, no painel **Scope,** expanda o nó **Jobs** e clique em **Agendamento**. O painel **de ações** apresenta as ações disponíveis para o nó **agendado.**

![Ações painel de trabalhos programados exemplo](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, no painel **Scope,** expanda o nó **Jobs,** clique em **Agendar**e, em seguida, clique num trabalho agendado no painel **de Resultados.** O painel **de ações** apresenta as ações disponíveis para o trabalho programado, como mostra o exemplo seguinte.

![Ações pane ações de trabalho exemplo](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navegação de teclado e atalhos
O StorSimple Snapshot Manager permite as funcionalidades de acessibilidade do sistema operativo Windows e da Consola de Gestão da Microsoft (MMC). Também inclui algumas funcionalidades de navegação de teclado e atalhos específicos do StorSimple Snapshot Manager, conforme descrito nas seguintes secções.

* [Chaves de navegação de teclado](#keyboard-navigation-keys) 
* [Chaves de atalho da barra de menu](#menu-bar-shortcut-keys) 
* [Chaves de atalho de painel de âmbito](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Chaves de navegação de teclado
A tabela a seguir descreve as teclas que pode utilizar para navegar na interface de utilizador StorSimple Snapshot Manager. 

| Chave de navegação | Ação |
|:--- |:--- |
| Tecla de seta para baixo |Utilize a tecla de seta para baixo para mover-se verticalmente para o próximo item num menu ou painel. |
| ENTER |Pressione a tecla 'Entrar' para completar uma ação e, em seguida, avançar para o passo seguinte. Por exemplo, pode premir Enter para selecionar **Next**, **OK**, ou **Criar**, e, em seguida, ir para o próximo passo num assistente. |
| Esc |Pressione a tecla Esc para fechar um menu ou cancelar e fechar uma página. |
| F1 |Prima a tecla F1 para ver um tópico de ajuda para a janela atualmente ativa. |
| F5 |Pressione a tecla F5 para refrescar um nó. |
| F6 |Pressione a tecla F6 para passar do painel **de âmbito** para o painel **de resultados.** |
| F10 |Pressione a tecla F10 para ir à barra de menus. |
| Tecla Seta Para a Esquerda |Utilize a tecla de seta esquerda para mover horizontalmente de uma opção de barra de menu para a opção anterior. Quando se desloca para o item anterior na barra de menus, aparece o menu de ação (ou contexto) para o artigo anterior. |
| Tecla Seta Para a Direita |Utilize a tecla de seta direita para mover horizontalmente de uma opção de barra de menu para a seguinte. Quando se desloca para o próximo item na barra de menu, aparece o menu de ação (ou contexto) para o novo artigo. |
| Chave do separador |Utilize a tecla 'Guia' para passar para o painel seguinte da consola ou para a próxima seleção ou caixa de texto numa página. |
| Tecla de seta para cima |Utilize a tecla de seta para cima para se mover verticalmente para o item anterior num menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Chaves de atalho da barra de menu
A tabela seguinte descreve as combinações de chaves de atalho para a barra de menu. Depois de premir as teclas de atalho e o menu abrir, pode utilizar as teclas de atalho do menu (as teclas sublinhadas no menu). Para mais informações sobre a barra de menu, vá ao [menu bar.](#menu-bar)

| Atalho | Resultado | Chave de atalho do menu | Resultado |
|:--- |:--- |:--- |:--- |
| ALT+F |Abre o menu **'Ficheiro'.** |N |Abre uma nova instância de consola. |
|  |O |Abre a página **Ferramentas Administrativas.** | |
|  |S |Guarda a consola StorSimple Snapshot Manager. | |
|  |A |Abre a página **Save As.** | |
|  |M |Abre a página **Add/Remove Snap-in.** | |
|  |P |Abre a página **Opções.** | |
|  |H |Abre ajuda online. | |
| ALT+A |Abre o menu **Ação.** |I |Liga e desliga a opção de exibição de importação. |
|  |W |Abre uma nova consola StorSimple Snapshot Manager. | |
|  |F |Atualiza a consola StorSimple Snapshot Manager. | |
|  |L |Abre a página **Lista de Exportação.** | |
|  |H |Abre ajuda online. | |
| ALT+V |Abre o menu **Ver.** |A |Abre a página **'Adicionar/Remover Colunas'.** |
|  |U |Abre a página **'Visualizar' Personalizado.** | |
| ALT+O |Abre o menu **Favoritos.** |A |Abre a página **Adicionar aos Favoritos.** |
|  |O |Abre a página **"Organizar Favoritos".** | |
| ALT+W |Abre o menu **Janela.** |N |Abre outra janela do StorSimple Snapshot Manager. |
|  |C |Exibe todas as janelas de consola abertas num estilo em cascata. | |
|  |T |Exibe todas as janelas de consola abertas num padrão de grelha. | |
|  |I |Organiza ícones numa linha horizontal na parte inferior do ecrã. | |
| ALT+H |Abre o menu **Ajuda.** |H |Abre ajuda online. |
|  |T |Abre a página web do Microsoft TechNet Tech Center. | |
|  |A |Abre a página sobre a **consola de gestão da Microsoft.** | |

### <a name="scope-pane-shortcut-keys"></a>Chaves de atalho de painel de âmbito
As tabelas a seguir mostram as combinações de chave de atalho para cada nó no **painel Scope.** 

* [Chaves de atalho de nó de dispositivos](#devices-node-shortcut-keys)
* [Chaves de atalho de nó de volumes](#volumes-node-shortcut-keys)
* [Chaves de atalho de atalho de nó grupos de volume](#volume-groups-node-shortcut-keys)
* [Chaves de atalho de atalho de atalho de atalho de notalho de políticas](#backup-policies-node-shortcut-keys)
* [Chaves de atalho de atalho de nó de catálogo de cópias de segurança](#backup-catalog-node-shortcut-keys)
* [Chaves de atalho de nó de emprego](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Chaves de atalho de nó de dispositivos
| Atalho do menu | Resultado |
|:--- |:--- |
| C |Abre a página **Configurar um Dispositivo.** |
| D |Refresca a lista de dispositivos e detalhes do dispositivo. |
| V |Abre o menu **Ver.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Details.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página **Lista de Exportação.** |
| H |Abre ajuda online. |

#### <a name="volumes-node-shortcut-keys"></a>Chaves de atalho de nó de volumes
| Atalho do menu | Resultado |
|:--- |:--- |
| V |Atualiza a lista de volumes. |
| V (prima duas vezes) |Abre o menu **Ver.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Volumes.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página **Lista de Exportação.** |
| H |Abre ajuda online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Chaves de atalho de atalho de nó grupos de volume
| Atalho do menu | Resultado |
|:--- |:--- |
| G |Abre a página **Criar um Grupo de Volume.** |
| V |Abre o menu **Ver.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Grupos de Volume.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página **Lista de Exportação.** |
| H |Abre ajuda online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Chaves de atalho de atalho de atalho de atalho de notalho de políticas
| Atalho do menu | Resultado |
|:--- |:--- |
| B |Abre a página **Criar uma Política.** |
| V |Abre o menu **Ver.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Grupos de Volume.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página **Lista de Exportação.** |
| H |Abre ajuda online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Chaves de atalho de atalho de nó de catálogo de cópias de segurança
| Atalho do menu | Resultado |
|:--- |:--- |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Grupos de Volume.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| H |Abre ajuda online. |

#### <a name="jobs-node-shortcut-keys"></a>Chaves de atalho de nó de emprego
| Atalho do menu | Resultado |
|:--- |:--- |
| V |Abre o menu **Ver.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Jobs.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página **Lista de Exportação.** |
| H |Abre ajuda online |

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o StorSimple Snapshot Manager para ligar e gerir dispositivos](storsimple-snapshot-manager-manage-devices.md).

