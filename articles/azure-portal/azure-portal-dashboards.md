---
title: Criar e partilhar dashboards no portal Azure
description: Este artigo descreve como criar, personalizar, publicar e compartilhar painéis no portal do Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901034"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e partilhar dashboards no portal Azure

Os dashboards são uma visão focada e organizada dos seus recursos em nuvem no portal Azure. Utilize os dashboards como um espaço de trabalho onde pode lançar rapidamente tarefas para operações diárias e monitorizar os recursos. Construir dashboards personalizados com base em projetos, tarefas ou funções de utilizador, por exemplo.

O portal do Azure fornece um painel padrão como um ponto de partida. Pode editar o painel de instrumentos predefinido. Crie e personalize dashboards adicionais e publique e partilhe dashboards para disponibilizá-los a outros utilizadores. Este artigo descreve como criar um novo painel, personalizar a interface e publicar e compartilhar painéis.

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard

Neste exemplo, criamos um novo painel privado e atribuímos um nome. Siga estes passos para começar:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. A partir do menu do portal Azure, selecione **Dashboard**. O modo de exibição padrão já pode estar definido como Dashboard.

    ![Abra o painel de instrumentos](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecione **Novo painel de instrumentos**.

    ![Captura de tela do painel padrão](./media/azure-portal-dashboards/create-new-dashboard.png)

    Essa ação abre a **Galeria**de blocos, na qual você selecionará blocos e uma grade vazia na qual você organizará os blocos.

    ![Captura de tela da Galeria de blocos e da grade vazia](./media/azure-portal-dashboards/dashboard-name.png)

1. Selecione o texto **meu painel** no rótulo do painel e insira um nome que o ajudará a identificar facilmente o painel personalizado.

1. Selecione **personalização concluída** no cabeçalho da página para sair do modo de edição.

Agora, o modo de exibição de painel mostra o painel vazio. Selecione a seta ao lado do nome do painel de instrumentos para ver os dashboards disponíveis para si. A lista pode incluir dashboards que outros utilizadores criaram e partilharam.

## <a name="edit-a-dashboard"></a>Editar um dashboard

Agora, vamos editar o painel para adicionar, redimensionar e organizar os blocos que representam os recursos do Azure.

### <a name="add-tiles"></a>Adicione azulejos

Para adicionar blocos a um painel, siga estas etapas:

1. Selecione ![editar ícone](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** a partir do cabeçalho da página.

    ![Captura de tela do realce de painel Editar](./media/azure-portal-dashboards/dashboard-edit.png)

1. Navegue pela **Galeria de blocos** ou use o campo de pesquisa para localizar o bloco desejado.

1. **Selecione Adicionar** para adicionar o azulejo ao painel de instrumentos com um tamanho e localização predefinidos. Ou arraste o bloco até a grade e coloque-o onde desejar.

Muitas páginas de recursos incluem um ícone pushpin na barra de comando. Se você selecionar o ícone, um bloco que representa a página de origem será fixado no painel ativo no momento. Esse método é uma maneira alternativa de adicionar blocos ao seu painel.

![Captura de tela da barra de comandos da página com ícone de fixação](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Se você trabalhar com mais de uma organização, adicione o bloco **identidade da organização** ao seu painel para mostrar claramente a qual organização os recursos pertencem.
>
>

### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar blocos

Para alterar o tamanho de um bloco ou reorganizar os blocos em um painel, siga estas etapas:

1. Selecione ![editar ícone](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** a partir do cabeçalho da página.

1. Selecione o menu de contexto no canto superior direito de um bloco. Em seguida, escolha um tamanho de bloco. Blocos que dão suporte a qualquer tamanho também incluem um "identificador" no canto inferior direito que permite que você arraste o bloco para o tamanho desejado.

    ![Captura de tela do painel com o menu tamanho do bloco aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selecione um bloco e arraste-o para um novo local na grade para organizar seu painel.

### <a name="additional-tile-configuration"></a>Configuração de bloco adicional

Alguns azulejos podem requerer mais configuração para mostrar a informação que deseja. Por exemplo, o bloco de **gráfico de métricas** precisa ser configurado para exibir uma métrica de **Azure monitor**. Você também pode personalizar os dados do bloco para substituir as configurações de tempo padrão do painel.

Qualquer bloco que precise ser configurado exibe uma faixa de **blocos de configuração** até que você personalize o bloco. Selecione essa faixa e, em seguida, faça a configuração necessária.

![Captura de tela do bloco que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um bloco de redução permite que você exiba conteúdo estático e personalizado em seu painel. Isso pode ser instruções básicas, uma imagem, um conjunto de hiperlinks ou até mesmo informações de contato. Para obter mais informações sobre a utilização de um azulejo de marcação, consulte [Utilize um azulejo de marcação nos painéis Do Azure para mostrar conteúdo personalizado](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personalizar dados de bloco

Os dados no painel mostram automaticamente a atividade das últimas 24 horas. Para mostrar um período de tempo diferente apenas para este bloco, siga estas etapas:

1. Selecione Personalizar dados de **azulejos** do menu de contexto ou do ícone do filtro ![](./media/azure-portal-dashboards/dashboard-filter.png) filtro do canto superior esquerdo do azulejo.

    ![Captura de tela do menu de contexto do bloco](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Marque a caixa de seleção para **substituir as configurações de hora do painel no nível do bloco**.

    ![Captura de tela da caixa de diálogo para definir as configurações de tempo do bloco](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Escolha o período de tempo a ser mostrado para este bloco. Você pode escolher entre os últimos 30 minutos até os últimos 30 dias ou definir um intervalo personalizado.

1. Escolha a granularidade de tempo a ser exibida. Você pode mostrar qualquer lugar de incrementos de um minuto para um mês.

1. Selecione **Aplicar**.

## <a name="delete-a-tile"></a>Excluir um bloco

Para remover um bloco de um painel, siga estas etapas:

* Selecione o menu de contexto no canto superior direito do bloco e, em seguida, selecione **remover do painel**. Ou,

* Selecione ![editar o ícone](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** para entrar no modo de personalização. Paire no canto superior direito do azulejo e, em seguida, selecione o ![eliminar o ícone](./media/azure-portal-dashboards/dashboard-delete-icon.png) apagar o ícone para remover o azulejo do painel de instrumentos.

   ![Captura de tela mostrando como remover bloco do painel](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clone um painel de instrumentos

Para usar um painel existente como modelo para um novo painel, siga estas etapas:

1. Verifique se o modo de exibição de painel está mostrando o painel que você deseja copiar.

1. No cabeçalho da página, selecione ![ícone de clone](./media/azure-portal-dashboards/dashboard-clone.png) **Clone**.

1. Uma cópia do painel de instrumentos, chamada **Clone do** seu nome de *painel,* abre no modo de edição. Use as etapas anteriores neste artigo para renomear e personalizar o painel.

## <a name="publish-and-share-a-dashboard"></a>Publicar e compartilhar um painel

Quando você cria um painel, ele é privado por padrão, o que significa que você é o único que pode vê-lo. Para disponibilizar os painéis para outras pessoas, você pode compartilhá-los com outros usuários. Primeiro, você precisa publicar o painel como um recurso do Azure. Para publicar e compartilhar um painel personalizado, siga estas etapas:

1. Selecione ![ícone de partilha](./media/azure-portal-dashboards/dashboard-share-icon.png) **Partilhar** a partir do cabeçalho da página. **Partilha + controlo** de acesso abre.

1. Verifique se o nome correto do painel é mostrado.

1. Selecione um **nome de assinatura**. Os usuários com acesso à assinatura podem usar o painel compartilhado. O acesso aos recursos representados pelos blocos individuais é determinado pelo controle de acesso baseado em função do Azure.

1. Marque a caixa de seleção para publicar este painel no grupo de recursos ' painéis ' da assinatura selecionada. Ou então, desmarque a caixa de seleção e escolha publicar em um grupo de recursos existente.

1. Escolha um local para o recurso de painel. Recomendamos que você localize o painel com outros recursos. Se escolher entre os grupos de recursos existentes, o painel de instrumentos está automaticamente localizado com esse grupo de recursos.

1. Selecione **Publicar**.

    ![Captura de tela da caixa de diálogo de publicação do Dashboard](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Definir o controle de acesso em um painel compartilhado

Depois que o painel for publicado, gerencie quem tem acesso ao painel seguindo estas etapas:

1. No painel **compartilhamento + controle de acesso** , selecione **gerenciar usuários**.

    ![Captura de tela da caixa de diálogo compartilhamento de painel + controle de acesso](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. A página **controle de acesso** é aberta. Pode rever o nível de acesso de alguém ou adicionar uma nova atribuição de funções. Ao adicionar uma atribuição de função aqui, você está concedendo permissões ao painel.

> [!NOTE]
> Os blocos são exibições representativas de recursos em sua organização. O acesso a recursos é gerenciado por meio da atribuição de controle de acesso baseado em função e as permissões são herdadas da assinatura para o recurso. Dar acesso a um dashboard não atribui automaticamente permissões aos recursos mostrados no painel de instrumentos. Para obter mais informações sobre permissões a dashboards partilhados e controlo de acesso baseado em papéis para recursos, consulte [dashboards Share Azure utilizando o Controlo de Acesso baseado em funções.](azure-portal-dashboard-share-access.md)

### <a name="open-a-shared-dashboard"></a>Abrir um painel compartilhado

Para localizar e abrir um painel compartilhado, siga estas etapas:

1. Selecione a seta ao lado do nome do painel de instrumentos.

1. Selecione na lista exibida de painéis ou **procure todos os painéis** se o painel que você deseja abrir não estiver listado.

    ![Captura de tela do menu de seleção do painel](./media/azure-portal-dashboards/dashboard-browse.png)

1. No campo **tipo** , selecione **painéis compartilhados**.

1. Selecione uma ou mais assinaturas. Você também pode inserir texto para filtrar os painéis por nome.

1. Selecione um painel na lista de painéis compartilhados.

## <a name="delete-a-dashboard"></a>Eliminar um dashboard

Para excluir permanentemente um painel particular ou compartilhado, siga estas etapas:

1. Selecione o painel de instrumentos que pretende eliminar da lista ao lado do nome do painel de instrumentos.

1. Selecione ![eliminar o ícone](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Apagar** a partir do cabeçalho da página.

1. Para um painel particular, selecione **OK** na caixa de diálogo de confirmação para remover o painel. Para um painel compartilhado, na caixa de diálogo de confirmação, marque a caixa de seleção para confirmar se o painel publicado não será mais visível para outras pessoas. Em seguida, selecione **OK**.

    ![Captura de tela da confirmação de exclusão](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passos seguintes

* [Partilhe dashboards Azure utilizando o Controlo de Acesso baseado em funções](azure-portal-dashboard-share-access.md)
* [Criar programaticamente painéis do Azure](azure-portal-dashboards-create-programmatically.md)
