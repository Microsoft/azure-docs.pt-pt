---
title: Interface de utilizador do StorSimple Snapshot Manager [ StorSimple Snapshot Manager ] Microsoft Docs
description: Descreve a interface de utilizador do StorSimple Snapshot Manager e explica como usá-la para gerir trabalhos de backup e o catálogo de backup.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933980"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Utilize a interface de utilizador do StorSimple Snapshot Manager para gerir trabalhos de backup e catálogo de backup

## <a name="overview"></a>Descrição geral
O StorSimple Snapshot Manager tem uma interface de utilizador intuitiva que pode utilizar para pegar e gerir backups. Este tutorial fornece uma introdução à interface do utilizador e explica como usar cada um dos componentes. Para obter uma descrição detalhada do StorSimple Snapshot Manager, consulte [o que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição da consola
Para visualizar a interface do utilizador, clique no ícone StorSimple Snapshot Manager no seu ambiente de trabalho. A janela da consola aparece, como mostra a seguinte ilustração.

![Pans storSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela da consola tem cinco elementos principais. Clique no link apropriado para obter uma descrição completa de cada elemento.

* [Barra de menu](#menu-bar) 
* [Barra de ferramentas](#tool-bar) 
* [Painel de mira](#scope-pane) 
* [Painel de resultados](#results-pane) 
* [Painel de ações](#actions-pane) 

Além disso, o StorSimple Snapshot Manager suporta a navegação por [teclado e uma série de atalhos.](#keyboard-navigation-and-shortcuts)

### <a name="console-accessibility"></a>Acessibilidade das consolas
A interface de utilizador do StorSimple Snapshot Manager suporta as funcionalidades de acessibilidade fornecidas pelo sistema operativo Windows e pela Consola de Gestão da Microsoft (MMC), bem como alguns atalhos de teclado específicos do StorSimple Snapshot Manager. 

* Para uma descrição das funcionalidades de acessibilidade do Windows, vá a [atalhos de Teclado para Windows](https://support.microsoft.com/kb/126449). 
* Para uma descrição das funcionalidades de acessibilidade do MMC, vá à [Acessibilidade para MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Para uma descrição das funcionalidades de acessibilidade do StorSimple Snapshot Manager, vá à [navegação de Teclado e atalhos.](#keyboard-navigation-and-shortcuts)

## <a name="menu-bar"></a>Barra de menu
A barra de menu sintetiza menus [de Arquivo,](#file-menu) [Ação,](#action-menu) [Visualização,](#view-menu) [Favoritos,](#favorites-menu) [Janela](#window-menu)e [Ajuda.](#help-menu)

Clique em qualquer item na barra de menus para ver uma lista de comandos disponíveis nesse menu. O exemplo seguinte mostra o menu **'Ver'** selecionado na barra de menus.

![Ver menu selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Ficheiro
O menu **Ficheiro** contém comandos padrão da Consola de Gestão da Microsoft (MMC).

#### <a name="menu-access"></a>Acesso ao menu
Para ver o menu **'Ficheiro',** clique em **File** na barra de menus. Aparece o seguinte menu.

![Menu de ficheiros StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve itens que aparecem no menu **'Ficheiro'.**

| Item do menu | Descrição |
|:--- |:--- |
| Novo |Clique em **New** para criar uma nova consola com base no StorSimple Snapshot Manager. |
| Abrir |Clique em **Abrir** para abrir uma consola existente. |
| Guardar |Clique em **Guardar** para salvar a consola atual. |
| Guardar como |Clique em **Save As** para criar uma nova instância renomeada da consola atual. Utilize a opção **Save As** para personalizar uma vista e guardá-la para posterior recuperação. Por exemplo, pode criar snap-ins StorSimple Snapshot Manager que apontam para servidores específicos. |
| Adicionar/Remover snap-in |Clique em **Adicionar/Remover snap-in** para adicionar ou remover snap-ins e para organizar nós no painel **Scope.** Para mais informações, vá a [Adicionar, Remover e Organizar Snap-ins e Extensões em MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções |Clique em **Opções** para alterar o ícone da consola, especificar os modos e permissões de acesso ao utilizador ou eliminar ficheiros de consola para aumentar o espaço de disco disponível. |
| Lista de caminhos de arquivo |Clique num caminho na lista numerada para reabrir um ficheiro que abriu recentemente. |
| Exit |Clique em **Saída** para fechar o menu **'Ficheiro'.** |

### <a name="action-menu"></a>Menu de ação
Utilize o menu **Ação** para selecionar a partir das ações disponíveis. Os itens disponíveis dependem da seleção que fizer no painel **scope** ou no painel **de resultados.**

#### <a name="menu-access"></a>Acesso ao menu
Para ver o menu **Action,** faça um dos seguintes:

* Clique num item no **painel** scope ou no painel **de resultados.**
* Selecione um item no painel **scope** ou painel **de resultados** e, em seguida, clique em **Agir** na barra de menus. 

Por exemplo, se selecionar o nó superior no painel **Scope** e, em seguida, clicar à direita ou clicar em **Ação** na barra de menu, aparece o seguinte menu.

![Menu de ação do Gestor de Snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O painel **De Ações** (à direita da consola) contém a mesma lista de ações que o menu **Action.** Além disso, o painel **Ações** contém as opções do menu **Ver,** que lhe permitem criar uma visão personalizada do painel **de resultados.**

![Painel de ações com ver menu aberto](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte contém uma lista alfabética das ações do StorSimple Snapshot Manager. 

* A coluna **Action** lista as ações que pode realizar em nódosos e resultados. 
* A coluna **Navegação** explica como exibir o menu **action** apropriado para que possa selecionar a ação. Algumas ações aparecem em vários menus **de Ação.** Para estas ações, selecione uma opção **de navegação** a partir da lista de balas. 
* A coluna **Descrição** descreve como usar cada ação no menu **action** ou painel de ações, e explica o que faz.

> [!NOTE]
> Os menus **Action** pane e **Action** contêm opções adicionais, tais como **Visualização,** **Nova Janela daqui,** **Refresh,** **Export List**e **Ajuda.** Estas opções estão disponíveis como parte do MMC, e não são específicas para o StorSimple Snapshot Manager. A tabela inclui descrições destas opções.
> 
> 

| Ação | Navegação | Descrição |
|:--- |:--- |:--- |
| Autenticar |Clique no nó dos **Dispositivos** e clique num dispositivo no painel **resultados.** |Clique em **Authenticate** para introduzir a palavra-passe que configurapara o dispositivo. |
| Clone |Expandir **o Catálogo de Backup,** expandir cloud **snapshots,** clicar numa cópia de segurança datada e, em seguida, selecionar um volume no painel **resultados.** |Clique em **Clone** para criar uma cópia de um instantâneo em nuvem e guarde-o num local que designe. |
| Configurar um dispositivo |Clique no nó dos **Dispositivos.** |Clique em **Configurar um Dispositivo** para configurar um único dispositivo ou vários dispositivos para se ligar ao anfitrião do Windows. |
| Criar política de backup |Efetue uma das seguintes ações:<ul><li>Políticas de **backup**do clique à direita.</li><li>Clique ou expanda Grupos de **Volume,** e, em seguida, clique à direita num grupo de volume.</li><li>Clique ou expanda o Catálogo de **Backup,** e, em seguida, clique à direita num grupo de volume.</li></ul> |Clique em **Criar política** de backup para configurar uma cópia de segurança programada para um grupo de volume. |
| Criar grupo de volume |Efetue uma das seguintes ações:<ul><li>Clique no nó **dos Volumes** e, em seguida, clique à direita num volume no painel **resultados.**</li><li>Clique no nó dos Grupos de **Volume.**</li></ul> |Clique em **Criar Grupo de Volume** para atribuir volumes a um grupo de volume. |
| Eliminar |Clique num nó ou resultado (este item aparece em muitos menus de **Ação** e paineles de **ações.)** |Clique em **Apagar** para apagar o nó ou resultado que selecionou. Quando a caixa de diálogo de confirmação aparecer, confirme ou cancele a eliminação. |
| Detalhes |Clique no nó dos **Dispositivos** e, em seguida, clique à direita num dispositivo no painel **resultados.** |Clique em **Detalhes** para ver os detalhes da configuração para um dispositivo. |
| Editar |Clique em Políticas de **Backup**e, em seguida, clique à direita numa política no painel **de resultados.** |Clique em **Editar** para alterar a programação de backup para um grupo de volume. |
| Lista de Exportações |Clique em qualquer nó ou resultado (este item aparece em todos os menus de **Ação** e paineles de **Ações.)** |Clique na **Lista de Exportação** para guardar uma lista num ficheiro de valor separado de vírem (CSV). Em seguida, pode importar este ficheiro num pedido de análise da folha de cálculo. |
| Ajuda |Clique em qualquer nó ou resultado. (Este item aparece em todos os menus **de** **Ação** e as ações.) |Clique em **Ajudar** a abrir a Ajuda online numa janela separada do navegador. |
| Nova Janela a Partir Daqui |Clique em qualquer nó ou resultado (este item aparece em todos os menus de **Ação** e paineles de **Ações.)** |Clique em **Nova Janela daqui** para abrir uma nova janela StorSimple Snapshot Manager. |
| Atualizar |Clique em qualquer nó ou resultado (este item aparece em todos os menus de **Ação** e paineles de **Ações.)** |Clique em **Refresh** para atualizar a janela StorSimple Snapshot Manager atualmente apresentada. |
| Dispositivo de atualização |Clique no nó dos **Dispositivos** e clique num dispositivo no painel **resultados.** |Clique no **Dispositivo refresh** para sincronizar um dispositivo conectado específico com o StorSimple Snapshot Manager. |
| Dispositivos de atualização |Clique no nó dos **Dispositivos.** |Clique em **Dispositivos de Atualização** para sincronizar a sua lista de dispositivos conectados com o StorSimple Snapshot Manager. |
| Volumes rescanos |Clique no nó **volumes.** |Clique em **volumes Rescan** para atualizar a lista de volumes que aparecem no painel **resultados.** |
| Restauro |Expandir **o Catálogo de Backup,** expandir um grupo de volume, expandir **snapshots locais** ou **Cloud Snapshots,** e, em seguida, clicar na direita de uma cópia de segurança. |Clique em **Restaurar** para substituir os dados atuais do grupo de volume com os dados da cópia de segurança selecionada. |
| Pegue backup |Efetue uma das seguintes ações:<ul><li>Expandir **Grupos**de Volume , e, em seguida, clicar à direita num grupo de volume.</li><li>Expandir **o Catálogo**de Backup , e, em seguida, clicar à direita num grupo de volume.</li></ul> |Clique em **'Tomar backup'** para iniciar imediatamente um trabalho de backup. |
| Exibição de importações de alternância |Clique no nó superior no painel **Scope** (o nó **StorSimple Snapshot Manager** nos exemplos). |Clique no **Toggle Imports Display** para mostrar ou ocultar os grupos de volume e cópias de segurança associadas que foram importadas do painel de assistência ao StorSimple Device Manager. |

### <a name="view-menu"></a>Menu Ver
Utilize o menu **'Ver'** para criar uma visão personalizada do conteúdo do painel **de resultados.** O menu **'Ver'** contém **colunas de adição/remoção** e **personalizar** opções.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder ao menu **Ver** na barra de menus ou no painel **Ações.**

![Menu de visualização de gerente de instantâneo StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve itens que aparecem no menu **'Ver'.**

| Item do menu | Descrição |
|:--- |:--- |
| Adicionar/Remover Colunas |Clique em **Adicionar/Remover Colunas** para adicionar ou remover colunas no painel **resultados.** |
| Personalizar |Clique em **Personalizar** para mostrar ou esconder itens na janela da consola StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu favorito
Utilize o menu **Favoritos** para adicionar, remover e organizar as visualizações e tarefas da página que utiliza com frequência. 

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder ao menu **Favoritos** na barra de menus.

![Menu favoritos do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve itens que aparecem no menu **Favoritos.**

| Item do menu | Descrição |
|:--- |:--- |
| Adicionar aos Favoritos |Clique em **Adicionar aos Favoritos** para adicionar a visão atual à sua lista de favoritos. |
| Organizar Favoritos |Clique **em Organizar Favoritos** para organizar o conteúdo da sua pasta Favoritos. |

### <a name="window-menu"></a>Menu da janela
Utilize o menu **Janela** para adicionar e reorganizar as janelas da consola StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder ao menu **Janela** na barra de menus.

![Menu de janela storSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra as janelas que estão abertas atualmente. Clique em qualquer janela dessa lista para trazer a janela para o primeiro plano. 

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve os itens que aparecem no menu Janela.

| Item do menu | Descrição |
|:--- |:--- |
| Nova Janela |Clique em **Nova Janela** para abrir uma nova janela de consola (além da janela existente). |
| Cascata |Clique em **Cascade** para exibir as janelas das consolas abertas num estilo em cascata. |
| Azulejo horizontalmente |Clique em **Azulejo horizontalmente** para exibir as janelas da consola aberta num formato de azulejo (ou grelha). |
| Organizar ícones |Se tiver várias janelas de consola abertas e espalhadas pelo seu ambiente de trabalho, minimize-as e clique em **Ícones de Organização** para as organizar numa linha horizontal na parte inferior do ecrã. |

### <a name="help-menu"></a>Menu Ajuda
Utilize o menu **Ajuda** para ver a ajuda online disponível para o StorSimple Snapshot Manager e o MMC. Também pode visualizar informações sobre as versões de software MMC e StorSimple Snapshot Manager que estão atualmente instaladas no seu sistema. 

Pode aceder ao menu **ajuda** na barra de menus. Também pode aceder a tópicos de ajuda do StorSimple Snapshot Manager a partir do painel **de Ações.**

![Menu de ajuda do Gestor de Snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela seguinte descreve itens que aparecem no menu Ajuda.

| Item do menu | Descrição |
|:--- |:--- |
| Ajuda no StorSimple Snapshot Manager |Clique **em Ajuda no StorSimple Snapshot Manager** para abrir a ajuda do StorSimple Snapshot Manager numa janela separada. |
| Tópicos de Ajuda |Clique em **Tópicos de Ajuda** para abrir a ajuda on-line do MMC numa janela separada. |
| TechCenter Web Site |Clique no Web Site do **TechCenter** para abrir a página inicial do Microsoft TechNet Tech Center numa janela separada. |
| Sobre a Consola de Gestão microsoft |Clique em consola de **gestão** da Microsoft para ver qual a versão da Consola de Gestão da Microsoft instalada no seu sistema. |
| Sobre o StorSimple Snapshot Manager |Clique **em StorSimple Snapshot Manager** para ver qual a versão do snap-in instalada no seu sistema. |

## <a name="tool-bar"></a>Barra de ferramentas
A barra de ferramentas, localizada abaixo da barra de menus, contém ícones de navegação e tarefas. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições do ícone
A tabela seguinte descreve os ícones que aparecem na barra de ferramentas. 

| Ícone | Descrição |
|:--- |:--- |
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Clique no ícone da seta esquerda para voltar à página anterior. |
| ![Seta para a direita](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Clique na seta direita para ir para a página seguinte (se a seta estiver cinzenta, a ação não está disponível). |
| ![Ícone acima](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Clique no ícone para cima para subir um nível na árvore da consola **(a** vidraça scope). |
| ![Árvore de consola show/hide](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Clique no ícone da árvore da consola para mostrar ou esconder o painel **scope.** |
| ![Lista de exportação](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Clique no ícone da lista de exportação para exportar uma lista para um ficheiro CSV que especifique. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Clique no ícone de ajuda para abrir um tópico de ajuda online do MMC. |
| ![Painel de ações de show/couro](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Clique no ícone **do** painel de ações para mostrar ou ocultar o painel **de ações.** |

## <a name="scope-pane"></a>Painel de mira
O painel **scope** é o painel mais à esquerda no StorSimple Snapshot Manager UI. Contém a árvore da consola (ou nó) e é o principal mecanismo de navegação para o StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Estrutura do painel de âmbito
O painel **scope** contém uma série de objetos clicáveis (nós) organizados numa estrutura de árvores. 

![Painel de mira](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Para expandir ou colapsar um nó, clique no ícone da seta ao lado do nome do nó.
* Para ver o estado ou o conteúdo de um nó, clique no nome do nó. A informação aparece no painel **de resultados.** 

O painel **scope** contém os seguintes nódosos: 

* [Nó de dispositivos](#devices-node) 
* [Nó de volumes](#volumes-node) 
* [Nó dos Grupos de Volume](#volume-groups-node) 
* [Nó de Políticas de Backup](#backup-policies-node) 
* [Nó de Catálogo de Cópias](#backup-catalog-node) 
* [Nó de emprego](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas de painel de âmbito
Pode utilizar o painel **Scope** para completar uma ação num nó específico. Para selecionar uma tarefa, faça um dos seguintes:

* Clique no nó direito e, em seguida, selecione a tarefa a partir do menu que aparece.
* Clique no nó e, em seguida, clique em **Ação** na barra de menus. Selecione a tarefa a partir do menu que aparece.
* Clique no nó e, em seguida, selecione a ação no painel **Deações.**

Quando seleciona um nó e utiliza qualquer um destes métodos para ver uma lista de tarefas, apenas são mostradas as ações que podem ser executadas nesse nó.

### <a name="devices-node"></a>Nó de dispositivos
O nó **de Dispositivos** representa os dispositivos StorSimple e dispositivos virtuais StorSimple que estão ligados ao StorSimple Snapshot Manager. Selecione este nó para ligar e configurar um dispositivo e importar os seus volumes associados, grupos de volumes e cópias de cópias de backup existentes. Vários dispositivos podem ser ligados a um único hospedeiro.

* Para expandir o nó, clique no ícone da seta ao lado dos **Dispositivos**.
* Para ver um menu de ações disponíveis, clique no nó dos **Dispositivos** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de dispositivos configurados, clique em **Dispositivos** no painel **Scope.** A lista de dispositivos, juntamente com informações sobre cada dispositivo, aparece no painel **resultados.**

### <a name="volumes-node"></a>Nó de volumes
O nó **volumes** representa as unidades que correspondem aos volumes montados pelo hospedeiro, incluindo os descobertos através do iSCSI e os descobertos através de um dispositivo. Utilize este nó para visualizar a lista de volumes disponíveis e atribuir volumes individuais a grupos de volume.

* Para expandir o nó, clique no ícone da seta ao lado dos **Volumes**.
* Para ver um menu de ações disponíveis, clique no nó **volumes** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de volumes, clique em **Volumes** no painel **Scope.** A lista de volumes, juntamente com informações sobre cada volume, aparece no painel **de resultados.**

### <a name="volume-groups-node"></a>Nó dos Grupos de Volume
Os grupos de volume também são conhecidos como grupos de consistência. Cada grupo de volume é um conjunto de volumes relacionados com aplicações que ajudam a garantir a consistência da aplicação durante as operações de backup. Utilize o nó dos **Grupos de Volume** para configurar estes grupos e para obter backups interativos ou criar horários de backup. 

* Para expandir o nó, clique no ícone da seta ao lado dos **Grupos de Volume**.
* Para ver um menu de ações disponíveis, clique no nó dos **Grupos de Volume** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de grupos de volume, clique em **Grupos de Volume** no painel **scope.** A lista de grupos de volume, juntamente com informações sobre cada grupo de volume, aparece no painel **resultado.**

### <a name="backup-policies-node"></a>Nó de Políticas de Backup
As políticas de backup são horários de trabalho para instantâneos locais e em nuvem. Utilize o nó de Política de **Backup** para especificar com que frequência é criada uma cópia de segurança e quanto tempo deve ser mantida uma cópia de segurança. 

* Para expandir o nó, clique no ícone da seta ao lado das Políticas de **Backup**.
* Para ver um menu de ações disponíveis, clique no nó de Política de **Backup** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de políticas de backup, clique em Políticas de **Backup** no painel **Scope.** A lista de políticas de backup, juntamente com informações sobre cada política, aparece no painel **de resultados.**

> [!NOTE]
> Pode reter um máximo de 64 reforços.


### <a name="backup-catalog-node"></a>Nó de Catálogo de Cópias
O nó **de Backup Catalog** contém listas de cópias de segurança no local e fora do local dos volumes Azure StorSimple. Este nó é organizado por grupo de volume, e cada recipiente de grupo de volume contém estruturas separadas para instantâneos locais (o nó **local snapshot)** e instantâneos em nuvem (o nó **Cloud Snapshots).** Quando expandido, cada contentor de grupo de volume lista todos os backups bem sucedidos que foram tomados interativamente ou por uma política configurada.

* Para expandir o nó, clique no ícone da seta ao lado do **Catálogo de Cópias**de Segurança .
* Para ver um menu de ações disponíveis, clique no nó de **Backup Catalog** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de imagens de backup, clique em **Backup Catalog** no painel **Scope.** A lista de instantâneos, juntamente com informações sobre cada instantâneo, aparece no painel **de resultados.**

### <a name="local-snapshots-node"></a>Nó snapshots locais
O nó **Snapshots Local** lista instantâneos locais para um grupo de volume específico. O nó está localizado sob o nó do **Catálogo de Cópias de Segurança** no painel **Scope.** Os instantâneos locais são cópias ponto-a-tempo de dados de volume que são armazenados no dispositivo Azure StorSimple. Tipicamente, este tipo de backup pode ser criado e restaurado rapidamente. Pode usar uma foto local como faria com uma cópia de reserva local.

* Para expandir o nó, clique no ícone da seta ao lado de **Snapshots Locais**.
* Para ver um menu de ações disponíveis, clique no nó **Local Snapshots** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de instantâneos locais, clique em **Snapshots Locais** no painel **Scope.** A lista de instantâneos, juntamente com informações sobre cada instantâneo, aparece no painel **de resultados.**

### <a name="cloud-snapshots-node"></a>Nó snapshots de nuvem
O nó **Cloud Snapshots** lista imagens de nuvem para um grupo de volume específico. O nó está localizado sob o nó do **Catálogo de Cópias de Segurança** no painel **Scope.** Os instantâneos em nuvem são cópias ponto-a-tempo de dados de volume que são armazenados na nuvem. Um instantâneo em nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferente, fora do local. Os instantâneos em nuvem são particularmente úteis em cenários de recuperação de desastres.

* Para expandir o nó, clique no ícone da seta ao lado de **Cloud Snapshots**.
* Para ver um menu de ações disponíveis, clique no nó **Cloud Snapshots** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de imagens em nuvem, clique em **Cloud Snapshots** no painel **Scope.** A lista de instantâneos, juntamente com informações sobre cada instantâneo, aparece no painel **de resultados.**

### <a name="jobs-node"></a>Nó de emprego
O nó **Jobs** contém informações sobre trabalhos de reserva programados, em execução e recentemente concluídos. 

* Para expandir o nó, clique no ícone da seta ao lado de **Jobs**.
* Para ver um menu de ações disponíveis, clique no nó **Jobs** ou clique à direita em qualquer um dos nós que aparecem na vista expandida.
* Para ver uma lista de trabalhos programados, expandir o nó **Jobs** e, em seguida, clicar **no Scheduled**. A lista de trabalhos e informações previamente configurados sobre cada trabalho aparece no painel **de resultados.** 
* Para ver uma lista de trabalhos recentemente concluídos, expandir o nó **Jobs** e, em seguida, clicar em **Last 24 Hours**. Uma lista de trabalhos que foram concluídos nas últimas 24 horas aparece no painel **de resultados.** O painel **de resultados** também contém informações sobre cada trabalho concluído.
* Para ver uma lista de empregos que estão atualmente em execução, expanda o nó **Jobs** e, em seguida, clique em **Running**. A lista de empregos e informações atualmente em funcionamento sobre cada trabalho aparece no painel **de resultados.**

## <a name="results-pane"></a>Painel de resultados
O painel **de resultados** é o painel central no StorSimple Snapshot Manager UI. Contém listas e informações detalhadas sobre o estado do nó selecionado no painel **Scope.**

### <a name="example"></a>Exemplo
Para ver o seguinte exemplo, clique no nó dos **Grupos de Volume** no painel **Scope.** O painel **de resultados** apresenta uma lista de grupos de volume com detalhes sobre cada grupo.

![Painel de resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Pode configurar os detalhes mostrados no painel **resultados:** clique à direita num nó no painel **Scope,** clique em **Ver**, e, em seguida, clique em **Adicionar/Remover Colunas**.

## <a name="actions-pane"></a>Painel de ações
O painel **de ações** é o painel certo no StorSimple Snapshot Manager UI. Contém um menu de operações que pode realizar no nó, visualização ou dados que selecionar no painel **scope** ou no painel **de resultados.** O painel **de ações** contém os mesmos comandos que os menus **Action** que estão disponíveis para itens no painel **scope** e **resultados.** Para obter uma descrição de cada ação, consulte a tabela na secção **de menus ação.**

### <a name="examples"></a>Exemplos
Para ver o seguinte exemplo, no painel **Scope,** expanda o nó **Jobs** e clique **agendado**. O painel **de ações** exibe as ações disponíveis para o nó **agendado.**

![Exemplo de trabalhos programados](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, no painel **scope,** expandir o nó **Jobs,** clicar **agendado,** e, em seguida, clicar num trabalho programado no painel **resultados.** O painel **de ações** exibe as ações disponíveis para o trabalho programado, como mostra o seguinte exemplo.

![Ações painelações de trabalho exemplo](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navegação de teclado e atalhos
O StorSimple Snapshot Manager permite as funcionalidades de acessibilidade do sistema operativo Windows e da Consola de Gestão da Microsoft (MMC). Também inclui algumas funcionalidades de navegação de teclado e atalhos específicos do StorSimple Snapshot Manager, conforme descrito nas seguintes secções.

* [Teclas de navegação de teclado](#keyboard-navigation-keys) 
* [Chaves de atalho de barra de menu](#menu-bar-shortcut-keys) 
* [Teclas de atalho de painel de âmbito](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Teclas de navegação de teclado
A tabela seguinte descreve as teclas que pode utilizar para navegar na interface de utilizador do StorSimple Snapshot Manager. 

| Chave de navegação | Ação |
|:--- |:--- |
| Tecla de seta para baixo |Utilize a tecla de seta para baixo para se mover verticalmente para o item seguinte num menu ou painel. |
| ENTER |Pressione a tecla Enter para completar uma ação e, em seguida, passar para o passo seguinte. Por exemplo, pode pressionar Enter para selecionar **Next**, **OK**, ou **Create**, e depois passar para o próximo passo num assistente. |
| Esc |Prima a tecla Esc para fechar um menu ou para cancelar e fechar uma página. |
| F1 |Prima a tecla F1 para ver um tópico de ajuda para a janela atualmente ativa. |
| F5 |Pressione a tecla F5 para refrescar um nó. |
| F6 |Pressione a tecla F6 para passar do painel **Scope** para o painel **resultados.** |
| F10 |Pressione a tecla F10 para ir à barra de menus. |
| Tecla Seta Para a Esquerda |Utilize a tecla seta esquerda para se mover horizontalmente de uma opção de barra de menu para a opção anterior. Quando se muda para o item anterior na barra de menus, aparece o menu de ação (ou contexto) para o item anterior. |
| Tecla Seta Para a Direita |Utilize a tecla de seta direita para mover horizontalmente de uma opção de barra de menu para a seguinte. Quando passa para o item seguinte na barra de menus, aparece o menu de ação (ou contexto) para o novo item. |
| Tecla de separador |Utilize a tecla Tab para passar para o próximo painel na consola ou para a próxima seleção ou caixa de texto numa página. |
| Chave de seta para cima |Utilize a tecla de seta para cima para se mover verticalmente para o item anterior num menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Chaves de atalho de barra de menu
A tabela seguinte descreve as combinações de chaves de atalho para a barra de menu. Depois de premir as teclas de atalho e o menu abrir, pode utilizar as teclas de atalho do menu (as teclas sublinhadas no menu). Para mais informações sobre a barra de menus, vá à barra de [menu.](#menu-bar)

| Atalho | Resultado | Chave de atalho do menu | Resultado |
|:--- |:--- |:--- |:--- |
| ALT+F |Abre o menu **Arquivo.** |N |Abre uma nova instância de consola. |
|  |O |Abre a página **Ferramentas Administrativas.** | |
|  |S |Guarde a consola StorSimple Snapshot Manager. | |
|  |A |Abre a página **Save As.** | |
|  |M |Abre a página **Add/Remove Snap-in.** | |
|  |P |Abre a página **Opções.** | |
|  |H |Abre a Ajuda online. | |
| ALT+A |Abre o menu **Action.** |I |Liga e desliga a opção de visualização de importação. |
|  |W |Abre uma nova consola StorSimple Snapshot Manager. | |
|  |F |Atualiza a consola StorSimple Snapshot Manager. | |
|  |L |Abre a página da Lista de **Exportação.** | |
|  |H |Abre a Ajuda online. | |
| ALT+V |Abre o menu **'Ver'.** |A |Abre a página **Adicionar/Remover Colunas.** |
|  |U |Abre a página **Personalizar Visualização.** | |
| ALT+O |Abre o menu **favoritos.** |A |Abre a página **Adicionar aos Favoritos.** |
|  |O |Abre a página **"Organizar Favoritos".** | |
| ALT+W |Abre o menu **janela.** |N |Abre outra janela do StorSimple Snapshot Manager. |
|  |C |Exibe todas as janelas de consola abertas num estilo em cascata. | |
|  |T |Exibe todas as janelas de consola abertas num padrão de grelha. | |
|  |I |Organiza ícones numa linha horizontal na parte inferior do ecrã. | |
| ALT+H |Abre o menu **ajuda.** |H |Abre a Ajuda online. |
|  |T |Abre a página web do Microsoft TechNet Tech Center. | |
|  |A |Abre a página sobre a Consola de Gestão da **Microsoft.** | |

### <a name="scope-pane-shortcut-keys"></a>Teclas de atalho de painel de âmbito
As tabelas que se seguem mostram as combinações de chaves de atalho para cada nó no painel **Scope.** 

* [Teclas de atalho de nó de dispositivos](#devices-node-shortcut-keys)
* [Teclas de atalho de nó de volume](#volumes-node-shortcut-keys)
* [Teclas de atalho de grupos de volume](#volume-groups-node-shortcut-keys)
* [Teclas de atalho de reserva](#backup-policies-node-shortcut-keys)
* [Chaves de atalho de nó de catálogo de reserva](#backup-catalog-node-shortcut-keys)
* [Teclas de atalho do nó de emprego](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Teclas de atalho de nó de dispositivos
| Atalho de menu | Resultado |
|:--- |:--- |
| C |Abre a página **Configurar um Dispositivo.** |
| D |Atualiza a lista de dispositivos e detalhes do dispositivo. |
| V |Abre o menu **'Ver'.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Details.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página da Lista de **Exportação.** |
| H |Abre a Ajuda online. |

#### <a name="volumes-node-shortcut-keys"></a>Teclas de atalho de nó de volume
| Atalho de menu | Resultado |
|:--- |:--- |
| V |Atualiza a lista de volumes. |
| V (prima duas vezes) |Abre o menu **'Ver'.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **volumes.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página da Lista de **Exportação.** |
| H |Abre a Ajuda online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Teclas de atalho de grupos de volume
| Atalho de menu | Resultado |
|:--- |:--- |
| G |Abre a página **Criar um Grupo de Volume.** |
| V |Abre o menu **'Ver'.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **dos Grupos de Volume.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página da Lista de **Exportação.** |
| H |Abre a Ajuda online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Teclas de atalho de reserva
| Atalho de menu | Resultado |
|:--- |:--- |
| B |Abre a página **Criar uma Política.** |
| V |Abre o menu **'Ver'.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **dos Grupos de Volume.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a Lista de Exportação **página. |
| H |Abre a Ajuda online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Chaves de atalho de nó de catálogo de reserva
| Atalho de menu | Resultado |
|:--- |:--- |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **dos Grupos de Volume.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| H |Abre a Ajuda online. |

#### <a name="jobs-node-shortcut-keys"></a>Teclas de atalho do nó de emprego
| Atalho de menu | Resultado |
|:--- |:--- |
| V |Abre o menu **'Ver'.** |
| W |Abre uma nova consola StorSimple Snapshot Manager focada no nó **Jobs.** |
| F |Atualiza a consola StorSimple Snapshot Manager. |
| L |Abre a página da Lista de **Exportação.** |
| H |Abre a Ajuda Online |

## <a name="next-steps"></a>Passos seguintes
* Aprenda a utilizar o [StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Aprenda a utilizar o [StorSimple Snapshot Manager para ligar e gerir dispositivos](storsimple-snapshot-manager-manage-devices.md).

