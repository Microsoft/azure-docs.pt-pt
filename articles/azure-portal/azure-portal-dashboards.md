---
title: Criar e partilhar dashboards no portal do Azure
description: Este artigo descreve como criar, personalizar, publicar e partilhar dashboards no portal Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: b4241e51f186ea973702562ab27bb7f13a77de57
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089490"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e partilhar dashboards no portal do Azure

Os dashboards são uma visão focada e organizada dos seus recursos em nuvem no portal Azure. Utilize dashboards como um espaço de trabalho onde pode lançar rapidamente tarefas para operações do dia-a-dia e monitorizar recursos. Construa dashboards personalizados com base em projetos, tarefas ou funções de utilizador, por exemplo.

O portal Azure fornece um dashboard predefinido como ponto de partida. Pode editar o painel de instrumentos predefinido. Crie e personalize dashboards adicionais e publique e partilhe dashboards para os disponibilizar a outros utilizadores. Este artigo descreve como criar um novo dashboard, personalizar a interface e publicar e partilhar dashboards.

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard

Neste exemplo, criamos um novo painel privado e atribuímos um nome. Siga estes passos para começar:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu do portal Azure, selecione **Dashboard**. A sua visão padrão pode já estar definida para o painel de instrumentos.

    ![Abra o painel](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecione **Novo painel de instrumentos**.

    ![Screenshot do novo painel de instrumentos](./media/azure-portal-dashboards/create-new-dashboard.png)

    Esta ação abre a **Galeria Tile,** a partir da qual você irá selecionar azulejos, e uma grade vazia onde você vai organizar os azulejos.

    ![Screenshot da galeria de azulejos e grelha vazia](./media/azure-portal-dashboards/dashboard-name.png)

1. Selecione o texto **My Dashboard** na etiqueta do painel de instrumentos e introduza um nome que o ajudará a identificar facilmente o painel de instrumentos personalizado.

1. Selecione **Fazer a personalização** no cabeçalho da página para sair do modo de edição.

A vista do painel mostra agora o seu novo painel de instrumentos. Selecione a seta ao lado do nome do painel de instrumentos para ver os painéis de instrumentos disponíveis para si. A lista pode incluir dashboards que outros utilizadores criaram e partilharam.

## <a name="edit-a-dashboard"></a>Editar um dashboard

Agora, vamos editar o painel para adicionar, redimensionar e organizar azulejos que representam os seus recursos Azure.

### <a name="add-tiles-from-the-dashboard"></a>Adicione azulejos do tablier

Para adicionar azulejos a um painel de instrumentos, siga estes passos:

1. Selecione ![ o ícone de edição ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** a partir do cabeçalho da página.

    ![Screenshot do painel de instrumentos que destaca a edição](./media/azure-portal-dashboards/dashboard-edit.png)

1. Percorra a **Galeria Tile** ou use o campo de pesquisa para encontrar o azulejo que deseja.

1. **Selecione Adicionar** o azulejo ao painel de instrumentos com um tamanho e localização predefinidos. Ou arraste o azulejo para a grelha e coloque-o onde quiser.

> [!TIP]
> Se trabalhar com mais do que uma organização, adicione o azulejo **de identidade** da Organização ao seu painel de instrumentos para mostrar claramente a que organização os recursos pertencem.

### <a name="add-tiles-from-a-resource-page"></a>Adicione azulejos de uma página de recursos

Existe uma forma alternativa de adicionar azulejos ao seu painel de instrumentos. Muitas páginas de recursos incluem um ícone de pushpin na barra de comando. Se selecionar o ícone, um azulejo que representa a página de origem é fixado no painel de instrumentos que está atualmente ativo. 

![Screenshot da barra de comando de página com ícone pino](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar azulejos

Para alterar o tamanho de um azulejo ou para reorganizar os azulejos num tablier, siga estes passos:

1. Selecione ![ o ícone de edição ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** a partir do cabeçalho da página.

1. Selecione o menu de contexto no canto superior direito de um azulejo. Então, escolha um tamanho de azulejo. Os azulejos que suportam qualquer tamanho também incluem uma "pega" no canto inferior direito que permite arrastar o azulejo para o tamanho que deseja.

    ![Screenshot do painel de instrumentos com menu de tamanho de azulejo aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selecione um azulejo e arraste-o para um novo local na grelha para organizar o seu painel de instrumentos.

### <a name="additional-tile-configuration"></a>Configuração adicional de azulejos

Alguns azulejos podem necessitar de mais configuração para mostrar a informação que deseja. Por exemplo, o **azulejo de gráfico métrico** tem de ser configurado para apresentar uma métrica do **Monitor Azure**. Também pode personalizar dados de azulejos para anular as definições de tempo padrão do painel de instrumentos.

Qualquer telha que precise de ser configurada exibe uma bandeira **de azulejos configurados** até personalizar o azulejo. Para personalizar o azulejo:

1. Selecione **Fazer a personalização** no cabeçalho da página para sair do modo de edição.

1. Selecione o banner e, em seguida, faça a configuração necessária.

    ![Screenshot de azulejo que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um azulejo de marcação permite-lhe exibir conteúdo estático personalizado no seu painel de instrumentos. Isto pode ser instruções básicas, uma imagem, um conjunto de hiperligações, ou até mesmo informações de contacto. Para obter mais informações sobre a utilização de um azulejo de marcação, consulte [Utilize um azulejo de marcação nos dashboards Azure para mostrar conteúdo personalizado](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Personalizar dados de azulejos

Os dados do painel mostram automaticamente atividade nas últimas 24 horas. Para mostrar um tempo diferente para apenas este azulejo, siga estes passos:

1. **Selecione Personalizar os dados** de azulejos do menu de contexto ou o ![ filtro do ícone do filtro a partir do canto superior esquerdo do ](./media/azure-portal-dashboards/dashboard-filter.png) azulejo.

    ![Screenshot do menu de contexto de azulejos](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Selecione a caixa de verificação para **substituir as definições de tempo do painel de instrumentos ao nível do azulejo**.

    ![Screenshot do diálogo para configurar definições de tempo de azulejo](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Escolha o tempo para mostrar para este azulejo. Pode escolher entre os últimos 30 minutos e os últimos 30 dias ou definir uma gama personalizada.

1. Escolha a granularidade temporal a visualizar. Pode mostrar em qualquer lugar desde incrementos de um minuto até um mês.

1. Selecione **Aplicar**.

## <a name="delete-a-tile"></a>Eliminar um mosaico

Para remover um azulejo de um tablier, siga estes passos:

* Selecione o menu de contexto no canto superior direito do azulejo e, em seguida, **selecione Remover do painel de instrumentos**. Ou,

* ![Selecione editar ícone ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** para introduzir o modo de personalização. Passe no canto superior direito do azulejo e, em seguida, selecione o ícone de exclusão do ![ ícone de eliminação para remover o azulejo ](./media/azure-portal-dashboards/dashboard-delete-icon.png) do tablier.

   ![Screenshot mostrando como remover azulejo do tablier](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonar um dashboard

Para utilizar um dashboard existente como modelo para um novo painel de instrumentos, siga estes passos:

1. Certifique-se de que a vista do painel de instrumentos mostra o painel de instrumentos que pretende copiar.

1. No cabeçalho da página, selecione ![ clone do ícone do clone ](./media/azure-portal-dashboards/dashboard-clone.png) **Clone**.

1. Uma cópia do painel de instrumentos, chamado **Clone do** seu nome de painel de *instrumentos,* abre-se no modo de edição. Utilize os passos anteriores neste artigo para mudar o nome e personalizar o painel de instrumentos.

## <a name="publish-and-share-a-dashboard"></a>Publicar e partilhar um dashboard

Quando se cria um painel, é privado por defeito, o que significa que é o único que pode vê-lo. Para disponibilizar dashboards a outros, pode publicá-los e partilhá-los. Para obter mais informações, consulte [os dashboards Share Azure utilizando o controlo de acesso baseado em funções Azure](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Abra um painel partilhado

Para encontrar e abrir um dashboard partilhado, siga estes passos:

1. Selecione a seta ao lado do nome do painel de instrumentos.

1. Selecione a partir da lista de painéis de instrumentos apresentados. Se o painel de instrumentos que pretende abrir não estiver listado:

    1. **selecione Navegar em todos os dashboards**.

        ![Screenshot do menu de seleção do painel de instrumentos](./media/azure-portal-dashboards/dashboard-browse.png)

    1. No campo **Tipo,** selecione **Dashboards Partilhados**.

        ![Screenshot de todos os menus de seleção de dashboards](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selecione uma ou mais subscrições. Também pode introduzir texto para filtrar os dashboards pelo nome.

    1. Selecione um dashboard da lista de dashboards partilhados.

## <a name="delete-a-dashboard"></a>Eliminar um dashboard

Para eliminar permanentemente um dashboard privado ou partilhado, siga estes passos:

1. Selecione o painel de instrumentos que pretende eliminar da lista ao lado do nome do painel de instrumentos.

1. Selecione ![ eliminar ícone ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Apagar** do cabeçalho da página.

1. Para um painel de instrumentos privado, selecione **OK** no diálogo de confirmação para remover o painel de instrumentos. Para um dashboard partilhado, no diálogo de confirmação, selecione a caixa de verificação para confirmar que o painel de instrumentos publicado deixará de ser visualizado por outros. Em seguida, selecione **OK**.

    ![Screenshot da confirmação de exclusão](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passos seguintes

* [Partilhe tabliers Azure utilizando o controlo de acesso baseado em funções Azure](azure-portal-dashboard-share-access.md)
* [Criar programaticamente dashboards do Azure](azure-portal-dashboards-create-programmatically.md)
