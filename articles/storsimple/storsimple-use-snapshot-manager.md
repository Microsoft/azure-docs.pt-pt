---
title: Interface de utilizador do Snapshot Manager do StorSimple | Documentos da Microsoft
description: Descreve a interface de utilizador do Snapshot Manager do StorSimple e explica como usá-lo para gerir tarefas de cópia de segurança e o catálogo de cópia de segurança.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: ''
ms.openlocfilehash: 46225e5a332e035e4d1cc256e71c4b5d8686fd47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845278"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Interface de utilizador de utilizar o StorSimple Snapshot Manager para gerir tarefas de cópia de segurança e o catálogo de cópia de segurança

## <a name="overview"></a>Descrição geral
O StorSimple Snapshot Manager tem uma interface de usuário intuitiva que pode utilizar e gerir cópias de segurança. Este tutorial disponibiliza uma introdução à interface do usuário e, em seguida, explica como usar cada um dos componentes. Para obter uma descrição detalhada do Snapshot Manager do StorSimple, consulte [o que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição de consola
Para ver a interface do usuário, clique no ícone do Snapshot Manager do StorSimple no seu ambiente de trabalho. A janela da consola é apresentada, conforme mostrado na ilustração seguinte.

![Painéis do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela da consola tem cinco elementos principais. Clique no link apropriado para obter uma descrição completa de cada elemento.

* [Barra de menus](#menu-bar) 
* [Barra de ferramentas](#tool-bar) 
* [Painel de âmbito](#scope-pane) 
* [Painel de resultados](#results-pane) 
* [Painel de ações](#actions-pane) 

Além disso, o Snapshot Manager do StorSimple suporta [navegação e um número de atalhos de teclado](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Acessibilidade da consola
A interface de utilizador do Snapshot Manager do StorSimple suporta as funcionalidades de acessibilidade fornecidas pelo sistema operacional Windows e a consola de gestão da Microsoft (MMC), bem como alguns atalhos de teclado do Snapshot Manager do StorSimple específicos. 

* Para obter uma descrição dos recursos de acessibilidade do Windows, aceda a [atalhos de teclado para Windows](https://support.microsoft.com/kb/126449). 
* Para obter uma descrição dos recursos de acessibilidade do MMC, aceda a [acessibilidade para o MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Para obter uma descrição dos recursos de acessibilidade do Snapshot Manager do StorSimple, aceda a [navegação e os atalhos de teclado](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra de menus
A barra de menu na parte superior da janela do console contém [arquivo](#file-menu), [ação](#action-menu), [vista](#view-menu), [Favoritos](#favorites-menu), [janela](#window-menu), e [ajudar](#help-menu) menus.

Clique em qualquer item na barra de menu para ver uma lista de comandos disponíveis desse menu. A exemplo a seguir mostra a **vista** menu selecionado na barra de menus.

![Menu do modo de exibição selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Ficheiro
O **ficheiro** menu contém comandos padrão da consola de gestão da Microsoft (MMC).

#### <a name="menu-access"></a>Acesso ao menu
Para ver os **arquivo** menu, clique em **ficheiro** na barra de menus. É apresentado o menu seguinte.

![Menu ficheiro do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no **ficheiro** menu.

| Item do menu | Descrição |
|:--- |:--- |
| Novo |Clique em **New** para criar um novo console com base no Snapshot Manager do StorSimple. |
| Aberto |Clique em **abra** para abrir uma consola existente. |
| Guardar |Clique em **guardar** para guardar a consola atual. |
| Guardar como |Clique em **guardar como** para criar uma instância nova, nome mudada da consola atual. Utilize o **guardar como** opção Personalizar uma vista e guardá-lo para uma recuperação posterior. Por exemplo, pode criar o snap-ins do Snapshot Manager do StorSimple que apontam para servidores específicos. |
| Adicionar/Remover Snap-in |Clique em **Adicionar/Remover Snap-in** para adicionar ou remover snap-ins e para organizar os nós no **âmbito** painel. Para obter mais informações, aceda a [adicionar, remover e Organizar Snap-ins e extensões do MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções |Clique em **opções** para alterar o ícone de consola, especifique modos de acesso de utilizador e as permissões ou eliminar ficheiros da consola para aumentar o espaço em disco disponível. |
| Lista de caminhos de ficheiro |Clique num caminho da lista numerada para reabrir um ficheiro aberto recentemente. |
| Sair |Clique em **Exit** para fechar a **ficheiro** menu. |

### <a name="action-menu"></a>Menu de ação
Utilize o **ação** menu para selecionar a partir de ações disponíveis. Os itens disponíveis para si dependem a seleção que fizer no **âmbito** painel ou **resultados** painel.

#### <a name="menu-access"></a>Acesso ao menu
Para ver os **ação** menu, efetue um dos seguintes procedimentos:

* Com o botão direito num item na **âmbito** painel ou **resultados** painel.
* Selecione um item no **âmbito** painel ou **resultados** painel e, em seguida, clique **ação** na barra de menus. 

Por exemplo, se selecionar o nó superior na **âmbito** painel e, em seguida, botão direito do mouse ou clique em **ação** na barra de menus, é apresentado o menu de seguinte.

![Menu de ação do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O **ações** painel (no lado direito da consola) contém a mesma lista de ações como o **ação** menu. Além disso, o **ações** painel contém o **vista** opções de menu, que permitem-lhe criar uma vista personalizada do **resultados** painel.

![Abra o painel de ações com o menu Ver](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte contém uma lista alfabética de ações do Snapshot Manager do StorSimple. 

* O **ação** coluna apresenta uma lista de ações que pode realizar em nós e os resultados. 
* O **navegação** coluna explica como apresentar o adequado **ação** menu para que possa selecionar a ação. Algumas ações são apresentados em vários **ação** menus. Para estas ações, selecione um **navegação** opção na lista com marcas. 
* O **Descrição** coluna descreve como utilizar cada ação no **ação** menu ou no painel ações e explica o que ele faz.

> [!NOTE]
> O **ações** painel e **ação** menus contêm opções adicionais, como **vista**, **nova janela a partir daqui**,  **Atualize**, **exportar lista**, e **ajudar**. Essas opções estão disponíveis como parte da MMC e não são específicas para o StorSimple Snapshot Manager. A tabela inclui descrições dessas opções.
> 
> 

| Ação | Navegação | Descrição |
|:--- |:--- |:--- |
| Autenticar |Clique nas **dispositivos** nó e com o botão direito num dispositivo a **resultados** painel. |Clique em **Authenticate** para introduzir a palavra-passe que configurou para o dispositivo. |
| Clone |Expanda **catálogo de cópia de segurança**, expanda **instantâneos de Cloud**, clique numa cópia de segurança com data e, em seguida, selecione um volume no **resultados** painel. |Clique em **Clone** para criar uma cópia de um instantâneo de cloud e armazená-lo numa localização que designar. |
| Configurar um dispositivo |Com o botão direito a **dispositivos** nó. |Clique em **Configure um dispositivo** para configurar um único ou vários dispositivos para ligar ao anfitrião do Windows. |
| Criar política de cópia de segurança |Efetue uma das seguintes ações:<ul><li>Com o botão direito **políticas de cópia de segurança**.</li><li>Clique ou expanda **grupos de volumes**e, em seguida, clique com botão direito um grupo de volume.</li><li>Clique ou expanda **catálogo de cópia de segurança**e, em seguida, clique com botão direito um grupo de volume.</li></ul> |Clique em **criar política de cópia de segurança** para configurar uma cópia de segurança agendada para um grupo de volume. |
| Criar grupo de volumes |Efetue uma das seguintes ações:<ul><li>Clique nas **Volumes** nó e, em seguida, clique com botão direito um volume no **resultados** painel.</li><li>Com o botão direito a **grupos de volumes** nó.</li></ul> |Clique em **criar grupo de volumes** para atribuir volumes a um grupo de volume. |
| Eliminar |Clique num nó ou o resultado (este item é apresentado em muitos **ação** menus e **ações** painéis.) |Clique em **eliminar** para eliminar o nó ou o resultado que selecionou. Quando for apresentada a caixa de diálogo de confirmação, confirmar ou cancelar a eliminação. |
| Detalhes |Clique nas **dispositivos** nó e, em seguida, faça duplo clique num dispositivo a **resultados** painel. |Clique em **detalhes** para ver os detalhes de configuração para um dispositivo. |
| Editar |Clique em **políticas de cópia de segurança**e, em seguida, clique com botão direito uma política no **resultados** painel. |Clique em **editar** para alterar a agenda de cópia de segurança para um grupo de volume. |
| Exportar lista |Clique em qualquer nó ou o resultado (este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **exportar lista** para guardar uma lista num arquivo de valores separados por vírgulas (CSV). Em seguida, pode importar este ficheiro para um aplicativo de folha de cálculo para análise. |
| Ajuda |Clique em qualquer nó ou o resultado. (Este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **ajudar** para abrir a ajuda online numa janela de browser separados. |
| Janela novo a partir daqui |Clique em qualquer nó ou o resultado (este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **nova janela a partir daqui** para abrir uma nova janela do Snapshot Manager do StorSimple. |
| Atualizar |Clique em qualquer nó ou o resultado (este item é apresentado em todos os **ação** menus e **ações** painéis.) |Clique em **atualizar** para atualizar a janela do Snapshot Manager do StorSimple atualmente exibido. |
| Atualizar dispositivo |Clique nas **dispositivos** nó e com o botão direito num dispositivo a **resultados** painel. |Clique em **atualizar dispositivo** para sincronizar um dispositivo conectado específico com o StorSimple Snapshot Manager. |
| Atualizar dispositivos |Com o botão direito a **dispositivos** nó. |Clique em **atualizar dispositivos** para sincronizar a lista de dispositivos ligados com o StorSimple Snapshot Manager. |
| Reanalisar volumes |Com o botão direito a **Volumes** nó. |Clique em **reanalisar volumes** para atualizar a lista de volumes que aparece no **resultados** painel. |
| Restauro |Expanda **catálogo de cópia de segurança**, expanda o grupo de volumes, expanda **instantâneos locais** ou **instantâneos de Cloud**e, em seguida, clique com botão direito uma cópia de segurança. |Clique em **restaurar** para substituir os dados do grupo de volume atual com os dados da cópia de segurança selecionado. |
| Efetuar cópia de segurança |Efetue uma das seguintes ações:<ul><li>Expanda **grupos de volumes**e, em seguida, clique com botão direito um grupo de volume.</li><li>Expanda **catálogo de cópia de segurança**e, em seguida, clique com botão direito um grupo de volume.</li></ul> |Clique em **efetuar cópia de segurança** para iniciar uma tarefa de cópia de segurança imediatamente. |
| Exibição de importações de alternância |Com o botão direito no nó superior na **âmbito** painel (a **Snapshot Manager do StorSimple** nó nos exemplos). |Clique em **alternância Imports apresentar** para mostrar ou ocultar os grupos de volumes e cópias de segurança associadas que foram importadas a partir do dashboard de serviço do Gestor de dispositivos do StorSimple. |

### <a name="view-menu"></a>Menu Ver
Utilize o **View** menu para criar uma vista personalizada da **resultados** conteúdo do painel. O **View** menu contém **Add/Remove Columns** e **personalizar** opções.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder a **View** menu no menu de barra ou no **ações** painel.

![Menu do modo de exibição do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no **vista** menu.

| Item do menu | Descrição |
|:--- |:--- |
| Adicionar/remover colunas |Clique em **Adicionar/remover colunas** para adicionar ou remover colunas na **resultados** painel. |
| Personalizar |Clique em **personalizar** para mostrar ou ocultar itens na janela de consola do Snapshot Manager do StorSimple. |

### <a name="favorites-menu"></a>Menu Favoritos
Utilizar o **Favoritos** menu para adicionar, remover e organizar as vistas de página e tarefas com freqüência. 

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder a **Favoritos** menu na barra de menus.

![Menu Favoritos do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no **Favoritos** menu.

| Item do menu | Descrição |
|:--- |:--- |
| Adicionar aos Favoritos |Clique em **adicionar aos Favoritos** para adicionar a exibição atual à sua lista de Favoritos. |
| Organizar Favoritos |Clique em **organizar Favoritos** para organizar o conteúdo da sua pasta de Favoritos. |

### <a name="window-menu"></a>Menu da janela
Utilize o **janela** menu para adicionar e reorganiza as janelas de consola do Snapshot Manager do StorSimple.

#### <a name="menu-access"></a>Acesso ao menu
Pode aceder a **janela** menu na barra de menus.

![Menu da janela do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra as janelas que estão atualmente abertas. Clique em qualquer janela nessa lista para que passem a janela de primeiro plano. 

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no menu da janela.

| Item do menu | Descrição |
|:--- |:--- |
| Nova janela |Clique em **nova janela** para abrir uma nova janela de consola (além da janela existente). |
| Em cascata |Clique em **Cascade** para apresentar as janelas de consola aberta num estilo em cascata. |
| Mosaico horizontalmente |Clique em **mosaico horizontalmente** para apresentar as janelas de consola aberta num formato de mosaico (ou grade). |
| Dispor ícones |Se tiver vários consola windows abra e usuários espalhados por ambiente de trabalho, minimizá-los e, em seguida, clique em **dispor ícones** para colocá-los numa linha horizontal na parte inferior da tela. |

### <a name="help-menu"></a>Menu Ajuda
Utilize o **ajudar** menu para ver a ajuda online disponível do Snapshot Manager do StorSimple e o MMC. Também pode ver informações sobre as versões de software do MMC e do Snapshot Manager do StorSimple que estão actualmente instalados no seu sistema. 

Pode aceder a **ajudar** menu na barra de menus. Também pode aceder a tópicos de ajuda do Snapshot Manager do StorSimple desde o **ações** painel.

![Menu Ajuda do Snapshot Manager do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição de menu
A tabela seguinte descreve os itens que aparecem no menu de ajuda.

| Item do menu | Descrição |
|:--- |:--- |
| Ajuda para o StorSimple Snapshot Manager |Clique em **ajudar no Snapshot Manager do StorSimple** para abrir a ajuda do Snapshot Manager do StorSimple numa janela separada. |
| Tópicos de ajuda |Clique em **tópicos de ajuda** para abrir a ajuda online do MMC numa janela separada. |
| TechCenter Web Site |Clique em **TechCenter do Web Site** para abrir a home page do Microsoft TechNet Tech Center numa janela separada. |
| Sobre o Console de gerenciamento Microsoft |Clique em **sobre o Console de gerenciamento do Microsoft** para ver a versão da consola de gestão do Microsoft está instalada no seu sistema. |
| Sobre o Snapshot Manager do StorSimple |Clique em **sobre o StorSimple Snapshot Manager** para ver qual é a versão do snap-in estiver instalada no seu sistema. |

## <a name="tool-bar"></a>Barra de ferramentas
Barra de ferramentas, localizada abaixo da barra de menu, contém os ícones de navegação e tarefas. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições de ícone
A tabela seguinte descreve os ícones que aparecem na barra de ferramentas. 

| Ícone | Descrição |
|:--- |:--- |
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Clique no ícone de seta para a esquerda para regressar à página anterior. |
| ![Seta para a direita](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Clique na seta à direita para ir para a página seguinte (se a seta está a cinzento, a ação não está disponível). |
| ![Ícone](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Clique no ícone de cima subir um nível na árvore da consola (a **âmbito** painel). |
| ![Mostrar/Ocultar árvore de console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Clique no ícone de árvore do console de Mostrar/ocultar para mostrar ou ocultar o **âmbito** painel. |
| ![Exportar lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Clique no ícone de lista de exportação para exportar uma lista para um ficheiro CSV que especificar. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Clique no ícone de ajuda para abrir um tópico de ajuda online do MMC. |
| ![Mostrar/Ocultar painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Clique em Mostrar/ocultar **ações** ícone do painel para mostrar ou ocultar o **ações** painel. |

## <a name="scope-pane"></a>Painel de âmbito
O **âmbito** painel é o painel mais à esquerda na IU do Snapshot Manager do StorSimple. Ele contém a árvore de consola (ou nó) e é o mecanismo de navegação principal para o StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Estrutura do painel de âmbito
O **âmbito** painel contém uma série de objetos clicáveis (nós), organizada numa estrutura de árvore. 

![Painel de âmbito](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Para expandir ou recolher um nó, clique no ícone de seta junto ao nome do nó.
* Para ver o estado ou conteúdo de um nó, clique no nome de nó. As informações são apresentadas na **resultados** painel. 

O **âmbito** painel contém os seguintes nós: 

* [Nó dispositivos](#devices-node) 
* [Nó de volumes](#volumes-node) 
* [Nó de grupos de volume](#volume-groups-node) 
* [Nó de políticas de cópia de segurança](#backup-policies-node) 
* [Nó catálogo de cópia de segurança](#backup-catalog-node) 
* [Nó de tarefas](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas do painel de âmbito
Pode utilizar o **âmbito** painel para concluir uma ação num nó específico. Para selecionar uma tarefa, efetue um dos seguintes procedimentos:

* Com o botão direito no nó e, em seguida, selecione a tarefa no menu que aparece.
* Clique no nó e, em seguida, clique em **ação** na barra de menus. Selecione a tarefa no menu que aparece.
* Clique no nó e, em seguida, selecione a ação no **ações** painel.

Ao selecionar um nó e utilizar qualquer um dos seguintes métodos para ver uma lista de tarefas, são apresentadas apenas as ações que podem ser executadas nesse nó.

### <a name="devices-node"></a>Nó dispositivos
O **dispositivos** nó representa os dispositivos do StorSimple e os dispositivos virtuais StorSimple que estejam ligados para o StorSimple Snapshot Manager. Selecione este nó para ligar e configurar um dispositivo e importar os seus volumes associados, grupos de volumes e cópias de segurança existentes. Vários dispositivos podem ser ligados a um único host.

* Expandir o nó, clique no ícone de seta junto a **dispositivos**.
* Para ver um menu de ações disponíveis, com o botão direito a **dispositivos** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de dispositivos configurados, clique em **dispositivos** no **âmbito** painel. A lista de dispositivos, juntamente com informações sobre cada dispositivo, é apresentado na **resultados** painel.

### <a name="volumes-node"></a>Nó de volumes
O **Volumes** nó representa as unidades que correspondem aos volumes montados pelo host, incluindo aquelas descobertos através de iSCSI e os que são detetados através de um dispositivo. Utilize este nó para ver a lista de volumes disponíveis e atribuir volumes individuais para grupos de volumes.

* Expandir o nó, clique no ícone de seta junto a **Volumes**.
* Para ver um menu de ações disponíveis, com o botão direito a **Volumes** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de volumes, clique em **Volumes** no **âmbito** painel. A lista de volumes, juntamente com informações sobre cada volume, aparece no **resultados** painel.

### <a name="volume-groups-node"></a>Nó de grupos de volume
Grupos de volumes também são conhecidos como grupos de consistência. Cada grupo de volume é um conjunto de volumes relacionados com a aplicação que ajuda a garantir a consistência de aplicação durante as operações de cópia de segurança. Utilize o **grupos de volumes** nó para configurar estes grupos e para efetuar cópias de segurança interativas ou criar agendas de cópia de segurança. 

* Expandir o nó, clique no ícone de seta junto a **grupos de volumes**.
* Para ver um menu de ações disponíveis, com o botão direito a **grupos de volumes** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de grupos de volume, clique em **grupos de volumes** no **âmbito** painel. A lista de grupos de volumes, juntamente com informações sobre cada grupo de volumes, é apresentado na **resultados** painel.

### <a name="backup-policies-node"></a>Nó de políticas de cópia de segurança
As políticas de cópia de segurança são agendas de tarefas para o local e na cloud instantâneos. Utilize o **políticas de cópia de segurança** nó para especificar a frequência com que é criada uma cópia de segurança e o tempo que uma cópia de segurança deve ser mantido. 

* Expandir o nó, clique no ícone de seta junto a **políticas de cópia de segurança**.
* Para ver um menu de ações disponíveis, com o botão direito a **políticas de cópia de segurança** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista das políticas de cópia de segurança, clique em **políticas de cópia de segurança** no **âmbito** painel. A lista de políticas de cópia de segurança, juntamente com informações sobre cada política é apresentada no **resultados** painel.

> [!NOTE]
> Pode manter um máximo de 64 cópias de segurança.


### <a name="backup-catalog-node"></a>Nó catálogo de cópia de segurança
O **catálogo de cópia de segurança** nó contém listas de cópias de segurança no local e fora do local de volumes do StorSimple do Azure. Este nó está organizado por grupo de volumes e cada contentor do grupo de volume contém estruturas separadas para instantâneos locais (a **instantâneo Local**nó s) e instantâneos da cloud (a **instantâneos de Cloud** nó). Quando expandido, o contentor do grupo de cada volume lista todos os backups com êxito que foram tomados interativamente ou por uma política configurada.

* Expandir o nó, clique no ícone de seta junto a **catálogo de cópia de segurança**.
* Para ver um menu de ações disponíveis, com o botão direito a **catálogo de cópia de segurança** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de instantâneos de cópia de segurança, clique em **catálogo de cópia de segurança** no **âmbito** painel. A lista de instantâneos, juntamente com informações sobre cada instantâneo, é apresentado na **resultados** painel.

### <a name="local-snapshots-node"></a>Nó de instantâneos local
O **instantâneos locais** nó apresenta uma lista de instantâneos locais para um grupo de volume específico. O nó está localizado no **catálogo de cópia de segurança** nó a **âmbito** painel. Os instantâneos locais são cópias de ponto no tempo dos dados do volume que estão armazenados no dispositivo StorSimple do Azure. Normalmente, este tipo de cópia de segurança pode ser criado e restaurado rapidamente. Pode usar um instantâneo local, tal como faria com uma cópia de segurança local.

* Expandir o nó, clique no ícone de seta junto a **instantâneos locais**.
* Para ver um menu de ações disponíveis, com o botão direito a **instantâneos locais** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de instantâneos locais, clique em **instantâneos locais** no **âmbito** painel. A lista de instantâneos, juntamente com informações sobre cada instantâneo, é apresentado na **resultados** painel.

### <a name="cloud-snapshots-node"></a>Nó de instantâneos de cloud
O **instantâneos de Cloud** nó apresenta uma lista de instantâneos de cloud para um grupo de volume específico. O nó está localizado no **catálogo de cópia de segurança** nó a **âmbito** painel. Os instantâneos de cloud são cópias de ponto no tempo dos dados de volume que estão armazenados na cloud. Um instantâneo de cloud é equivalente a um instantâneo replicado num sistema de armazenamento diferentes, fora das instalações. Instantâneos de cloud são particularmente úteis em cenários de recuperação após desastre.

* Expandir o nó, clique no ícone de seta junto a **instantâneos de Cloud**.
* Para ver um menu de ações disponíveis, com o botão direito a **instantâneos de Cloud** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de instantâneos de cloud, clique em **instantâneos de Cloud** no **âmbito** painel. A lista de instantâneos, juntamente com informações sobre cada instantâneo, é apresentado na **resultados** painel.

### <a name="jobs-node"></a>Nó de tarefas
O **tarefas** nó contém informações sobre tarefas de cópia de segurança agendadas, em execução e concluídas recentemente. 

* Expandir o nó, clique no ícone de seta junto a **tarefas**.
* Para ver um menu de ações disponíveis, com o botão direito a **tarefas** nó ou clique com botão direito a qualquer um de nós que aparecem na vista expandida.
* Para ver uma lista de tarefas agendadas, expanda o **trabalhos** nó e clique em **agendada**. A lista de tarefas configuradas anteriormente e informações sobre cada tarefa é apresentado na **resultados** painel. 
* Para ver uma lista de tarefas concluídas recentemente, expanda o **trabalhos** nó e clique em **últimas 24 horas**. É apresentada uma lista de tarefas que foram concluídas nas últimas 24 horas no **resultados** painel. O **resultados** painel também contém informações sobre cada tarefa concluída.
* Para ver uma lista de tarefas atualmente em execução, expanda o **trabalhos** nó e clique em **em execução**. A lista de atualmente em execução de tarefas e informações sobre cada tarefa aparece no **resultados** painel.

## <a name="results-pane"></a>Painel de resultados
O **resultados** painel é o painel central na IU do Snapshot Manager do StorSimple. Ele contém listas e informações de estado detalhadas para o nó que selecionou no **âmbito** painel.

### <a name="example"></a>Exemplo
Para ver o exemplo seguinte, clique a **grupos de volumes** nó a **âmbito** painel. O **resultados** painel mostra uma lista de grupos de volumes com detalhes sobre cada grupo.

![Painel de resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Pode configurar os detalhes mostrados na **resultados** painel: um nó com o botão direito a **âmbito** painel, clique em **vista**e, em seguida, clique em **Adicionar/remover colunas** .

## <a name="actions-pane"></a>Painel de ações
O **ações** painel é o painel da direita, na IU do Snapshot Manager do StorSimple. Contém um menu de operações que pode efetuar no nó, vista ou dados que selecionar na **âmbito** painel ou **resultados** painel. O **ações** painel contém os mesmos comandos a **ação** menus que estão disponíveis para os itens no **âmbito** painel e **resultados** painel. Para obter uma descrição de cada ação, consulte a tabela a **ação** secção de menu.

### <a name="examples"></a>Exemplos
Para ver o exemplo seguinte, no **âmbito** painel, expanda o **trabalhos** nó e clique em **agendada**. O **ações** painel apresenta as ações disponíveis para o **agendada** nó.

![Exemplo de tarefas agendadas do painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, na **âmbito** painel, expanda o **trabalhos** nó, clique em **agendada**e, em seguida, clique numa tarefa agendada no **resultados** painel. O **ações** painel apresenta as ações disponíveis para a tarefa agendada, conforme mostrado no exemplo a seguir.

![Exemplo de ações de tarefas do painel de ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navegação do teclado e atalhos
Snapshot Manager do StorSimple permite que os recursos de acessibilidade do sistema operativo Windows e a consola de gestão da Microsoft (MMC). Ele também inclui alguns recursos de navegação do teclado e atalhos que são específicos para o Snapshot Manager do StorSimple, conforme descrito nas seções a seguir.

* [Chaves de navegação do teclado](#keyboard-navigation-keys) 
* [Menu de barra teclas de atalho](#menu-bar-shortcut-keys) 
* [Teclas de atalho do painel de âmbito](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Chaves de navegação do teclado
A tabela seguinte descreve as chaves que pode utilizar para navegar a interface de utilizador do Snapshot Manager do StorSimple. 

| Chave de navegação | Ação |
|:--- |:--- |
| Tecla de seta |Utilize a tecla de seta para baixo para mover-se verticalmente para o próximo item num menu ou painel. |
| Enter |Prima a tecla Enter para concluir uma ação e, em seguida, avance para o passo seguinte. Por exemplo, pode premir a tecla Enter para selecionar **próxima**, **OK**, ou **criar**e, em seguida, vá para a próxima etapa num assistente. |
| Esc |Prima a tecla Esc para fechar um menu ou para cancelar e fechar uma página. |
| F1 |Prima a tecla F1 para ver um tópico de ajuda para a janela atualmente ativo. |
| F5 |Prima a tecla F5 para atualizar um nó. |
| F6 |Prima a tecla F6 para mover do **âmbito** painel para o **resultados** painel. |
| F10 |Prima a tecla F10 para aceder à barra de menus. |
| Tecla de seta para a esquerda |Utilize a tecla de seta para a esquerda para mover horizontalmente de uma opção de barra de menu à opção anterior. Ao mover para o item anterior na barra de menus, é apresentado o menu de ação (ou contexto) para o item anterior. |
| Tecla de seta para a direita |Utilize a tecla de seta para a direita para mover horizontalmente de opção da barra de menu de um para a próxima. Ao mover para o próximo item na barra de menus, é apresentado o menu de ação (ou contexto) para o novo item. |
| Tecla de tabulação |Utilize a tecla Tab para mover para o painel seguinte na consola ou para a seguinte caixa de seleção ou texto numa página. |
| A tecla de seta |Utilize a tecla de seta para cima para mover verticalmente para o item anterior num menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Menu de barra teclas de atalho
A tabela seguinte descreve as combinações de teclas de atalho para a barra de menu. Depois de pressiona as teclas de atalho e abre o menu, pode usar teclas de atalho do menu (as chaves de sublinhado no menu). Para obter mais informações sobre a barra de menus, aceda a [barra de menus](#menu-bar).

| Atalho | Resultado | Tecla de atalho do menu | Resultado |
|:--- |:--- |:--- |:--- |
| ALT + F |Abre o **ficheiro** menu. |N |É aberta uma nova instância de consola. |
|  |O |Abre o **ferramentas administrativas** página. | |
|  |S |Salva o console do Snapshot Manager do StorSimple. | |
|  |A |Abre o **guardar como** página. | |
|  |M |Abre o **Adicionar/Remover Snap-in** página. | |
|  |P |Abre o **opções** página. | |
|  |H |Abre a ajuda online. | |
| ALT + A |Abre o **ação** menu. |I |Torna-se a opção de exibição de importação e desativar. |
|  |W |É aberto um novo console do Snapshot Manager do StorSimple. | |
|  |F |Atualiza a consola do Snapshot Manager do StorSimple. | |
|  |L |Abre o **exportar lista** página. | |
|  |H |Abre a ajuda online. | |
| ALT + V |Abre o **vista** menu. |A |Abre o **Adicionar/remover colunas** página. |
|  |U |Abre o **Personalizar vista** página. | |
| ALT + O |Abre o **Favoritos** menu. |A |Abre o **adicionar aos Favoritos** página. |
|  |O |Abre o **organizar Favoritos** página. | |
| ALT + W |Abre o **janela** menu. |N |Abre-se de outra janela do Snapshot Manager do StorSimple. |
|  |C |Apresenta todas as janelas de consola aberta num estilo em cascata. | |
|  |T |Mostra todas as janelas de consola aberta num padrão de grade. | |
|  |I |Organiza ícones numa linha horizontal na parte inferior da tela. | |
| ALT + H |Abre o **ajudar** menu. |H |Abre a ajuda online. |
|  |T |Abre a página de web do Microsoft TechNet Tech Center. | |
|  |A |Abre o **sobre o Console de gerenciamento do Microsoft** página. | |

### <a name="scope-pane-shortcut-keys"></a>Teclas de atalho do painel de âmbito
As tabelas seguintes mostram o atalho combinações de teclas para cada nó no **âmbito** painel. 

* [Teclas de atalho do nó dispositivos](#devices-node-shortcut-keys)
* [Teclas de atalho do nó de volumes](#volumes-node-shortcut-keys)
* [Teclas de atalho de nó de grupos de volume](#volume-groups-node-shortcut-keys)
* [Teclas de atalho do nó de políticas de cópia de segurança](#backup-policies-node-shortcut-keys)
* [Teclas de atalho do nó de catálogo de cópia de segurança](#backup-catalog-node-shortcut-keys)
* [Teclas de atalho do nó de tarefas](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Teclas de atalho do nó dispositivos
| Menu de atalho | Resultado |
|:--- |:--- |
| C |Abre o **Configure um dispositivo** página. |
| D |Atualiza a lista de dispositivos e detalhes do dispositivo. |
| V |Abre o **vista** menu. |
| W |É aberto um novo console do Snapshot Manager do StorSimple voltada para o **detalhes** nó. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online. |

#### <a name="volumes-node-shortcut-keys"></a>Teclas de atalho do nó de volumes
| Menu de atalho | Resultado |
|:--- |:--- |
| V |Atualiza a lista de volumes. |
| V (premir duas vezes) |Abre o **vista** menu. |
| W |É aberto um novo console do Snapshot Manager do StorSimple voltada para o **Volumes** nó. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Teclas de atalho de nó de grupos de volume
| Menu de atalho | Resultado |
|:--- |:--- |
| G |Abre o **criar um grupo de Volume** página. |
| V |Abre o **vista** menu. |
| W |É aberto um novo console do Snapshot Manager do StorSimple voltada para o **grupos de volumes** nó. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Teclas de atalho do nó de políticas de cópia de segurança
| Menu de atalho | Resultado |
|:--- |:--- |
| B |Abre o **criar uma política** página. |
| V |Abre o **vista** menu. |
| W |É aberto um novo console do Snapshot Manager do StorSimple voltada para o **grupos de volumes** nó. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre a * * exportar lista * * página. |
| H |Abre a ajuda online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Teclas de atalho do nó de catálogo de cópia de segurança
| Menu de atalho | Resultado |
|:--- |:--- |
| W |É aberto um novo console do Snapshot Manager do StorSimple voltada para o **grupos de volumes** nó. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| H |Abre a ajuda online. |

#### <a name="jobs-node-shortcut-keys"></a>Teclas de atalho do nó de tarefas
| Menu de atalho | Resultado |
|:--- |:--- |
| V |Abre o **vista** menu. |
| W |É aberto um novo console do Snapshot Manager do StorSimple voltada para o **tarefas** nó. |
| F |Atualiza a consola do Snapshot Manager do StorSimple. |
| L |Abre o **exportar lista** página. |
| H |Abre a ajuda online |

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o Snapshot Manager do StorSimple para ligar e gerir dispositivos](storsimple-snapshot-manager-manage-devices.md).

