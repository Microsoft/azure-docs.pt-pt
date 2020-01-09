---
title: Criar e compartilhar painéis de portal do Azure | Microsoft Docs
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
ms.date: 07/01/2019
ms.author: mblythe
ms.openlocfilehash: a3b4d7cb33bf0da0c4431d76a54644208ea6468f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640461"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e partilhar dashboards no portal do Azure

Os painéis fornecem uma maneira de criar uma exibição focalizada e organizada na portal do Azure de seus recursos de nuvem. Use painéis como um espaço de trabalho onde você pode iniciar tarefas rapidamente para operações diárias e monitorar recursos.  Crie painéis personalizados com base em projetos, tarefas ou funções de usuário, por exemplo.  O portal do Azure fornece um painel padrão como um ponto de partida. Você pode editar o painel padrão, criar e personalizar painéis adicionais e publicar e compartilhar painéis para torná-los disponíveis para outros usuários. Este artigo descreve como criar um novo painel, personalizar a interface e publicar e compartilhar painéis.

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard

Neste exemplo, criamos um novo painel privado e atribuímos um nome. Siga estes passos para começar:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **painel** na seção superior da barra lateral esquerda. O modo de exibição padrão já pode estar definido como Dashboard.
1. Selecione **+ novo painel**.

    ![Captura de tela do painel padrão](./media/azure-portal-dashboards/dashboard-new.png)

4. Essa ação abre a **Galeria**de blocos, na qual você selecionará blocos e uma grade vazia na qual você organizará os blocos.

    ![Captura de tela da Galeria de blocos e da grade vazia](./media/azure-portal-dashboards/dashboard-name.png)

5. Selecione o texto **meu painel** no rótulo do painel e insira um nome que o ajudará a identificar facilmente o painel personalizado.
1. Selecione **personalização concluída** no cabeçalho da página para sair do modo de edição.

Agora, o modo de exibição de painel mostra o painel vazio. Selecione o menu suspenso ao lado do nome do painel para ver os painéis disponíveis para você – a lista pode incluir painéis que outros usuários criaram e compartilharam.

## <a name="edit-a-dashboard"></a>Editar um dashboard

Agora, vamos editar o painel para adicionar, redimensionar e organizar os blocos que representam os recursos do Azure.

### <a name="add-tiles"></a>Adicionar mosaicos

Para adicionar blocos a um painel, siga estas etapas:
1. Selecione ![ícone Editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** no cabeçalho da página.

    ![Captura de tela do realce de painel Editar](./media/azure-portal-dashboards/dashboard-edit.png)

2. Navegue pela **Galeria de blocos** ou use o campo de pesquisa para localizar o bloco desejado.
1. Selecione **Adicionar** para adicionar automaticamente o bloco ao painel com um tamanho e um local padrão. Ou arraste o bloco até a grade e coloque-o onde desejar.

Muitas páginas de recursos (também conhecidas como "lâminas") incluem um ícone de pino na barra de comandos. Se você selecionar o ícone, um bloco que representa a página de origem será fixado no painel ativo no momento. Esse método é uma maneira alternativa de adicionar blocos ao seu painel.

![Captura de tela da barra de comandos da página com ícone de fixação](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Se você trabalhar com mais de uma organização, adicione o bloco **identidade da organização** ao seu painel para mostrar claramente a qual organização os recursos pertencem.
>
>
### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar blocos
Para alterar o tamanho de um bloco ou reorganizar os blocos em um painel, siga estas etapas:

1. Selecione ![ícone Editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** no cabeçalho da página.
1. Selecione o menu de contexto no canto superior direito de um bloco. Em seguida, escolha um tamanho de bloco. Blocos que dão suporte a qualquer tamanho também incluem um "identificador" no canto inferior direito que permite que você arraste o bloco para o tamanho desejado.

   ![Captura de tela do painel com o menu tamanho do bloco aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Selecione um bloco e arraste-o para um novo local na grade para organizar seu painel.

### <a name="additional-tile-configuration"></a>Configuração de bloco adicional

Alguns blocos podem precisar de mais configurações para mostrar as informações desejadas. Por exemplo, o bloco de **gráfico de métricas** precisa ser configurado para exibir uma métrica de **Azure monitor**. Você também pode personalizar os dados do bloco para substituir as configurações de tempo padrão do painel.

Qualquer bloco que precise ser configurado exibe uma faixa de **blocos de configuração** até que você personalize o bloco. Selecione essa faixa e, em seguida, faça a configuração necessária.

![Captura de tela do bloco que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um bloco de redução permite que você exiba conteúdo estático e personalizado em seu painel. Isso pode ser instruções básicas, uma imagem, um conjunto de hiperlinks ou até mesmo informações de contato. Para obter mais informações sobre como usar um bloco de redução, consulte [usar um bloco de redução personalizado](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personalizar dados de bloco

Os dados no painel mostram automaticamente a atividade das últimas 24 horas. Para mostrar um período de tempo diferente apenas para este bloco, siga estas etapas:

1. Selecione o ícone de filtro de ![](./media/azure-portal-dashboards/dashboard-filter.png) ícone de filtro no canto superior esquerdo do bloco ou selecione **personalizar dados de bloco** no menu de contexto.

   ![Captura de tela do menu de contexto do bloco](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Marque a caixa de seleção para **substituir as configurações de hora do painel no nível do bloco**.

   ![Captura de tela da caixa de diálogo para definir as configurações de tempo do bloco](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Escolha o período de tempo a ser mostrado para este bloco. Você pode escolher entre os últimos 30 minutos até os últimos 30 dias ou definir um intervalo personalizado.
1. Escolha a granularidade de tempo a ser exibida. Você pode mostrar qualquer lugar de incrementos de um minuto para um mês.
1. Selecione **Aplicar**.

## <a name="delete-a-tile"></a>Excluir um bloco

Para remover um bloco de um painel, siga estas etapas:

* Selecione o menu de contexto no canto superior direito do bloco e, em seguida, selecione **remover do painel**. Ou,
* Selecione ![ícone Editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Editar** para entrar no modo de personalização. Focalize o canto superior direito do bloco e, em seguida, selecione o ícone ![excluir](./media/azure-portal-dashboards/dashboard-delete-icon.png) ícone Excluir para remover o bloco do painel.

   ![Captura de tela mostrando como remover bloco do painel](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonar um dashboard

Para usar um painel existente como modelo para um novo painel, siga estas etapas:

1. Verifique se o modo de exibição de painel está mostrando o painel que você deseja copiar.
1. No cabeçalho da página, selecione ![ícone de clonagem](./media/azure-portal-dashboards/dashboard-clone.png) **clone**.
1. Uma cópia do painel, chamada "clone do *seu nome do painel*", é aberta no modo de edição. Use as etapas anteriores neste artigo para renomear e personalizar o painel.

## <a name="publish-and-share-a-dashboard"></a>Publicar e compartilhar um painel

Quando você cria um painel, ele é privado por padrão, o que significa que você é o único que pode vê-lo. Para disponibilizar os painéis para outras pessoas, você pode compartilhá-los com outros usuários. Primeiro, você precisa publicar o painel como um recurso do Azure. Para publicar e compartilhar um painel personalizado, siga estas etapas:

1. Selecione o ícone de compartilhamento de ![](./media/azure-portal-dashboards/dashboard-share-icon.png) **compartilhamento** no cabeçalho da página. O formulário **compartilhamento + controle de acesso** é exibido.
1. Verifique se o nome correto do painel é mostrado.
1. Selecione um **nome de assinatura**. Os usuários com acesso à assinatura podem usar o painel compartilhado. O acesso aos recursos representados pelos blocos individuais é determinado pelo controle de acesso baseado em função do Azure.
1. Marque a caixa de seleção para publicar este painel no grupo de recursos ' painéis ' da assinatura selecionada. Ou então, desmarque a caixa de seleção e escolha publicar em um grupo de recursos existente.
1. Escolha um local para o recurso de painel. Recomendamos que você localize o painel com outros recursos. Observação: se você escolher um dos grupos de recursos existentes, o painel será localizado automaticamente com esse grupo de recursos.
1. Selecione **Publicar**.

   ![Captura de tela da caixa de diálogo de publicação do Dashboard](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Definir o controle de acesso em um painel compartilhado

Depois que o painel for publicado, gerencie quem tem acesso ao painel seguindo estas etapas:

1. No painel **compartilhamento + controle de acesso** , selecione **gerenciar usuários**.

   ![Captura de tela da caixa de diálogo compartilhamento de painel + controle de acesso](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. A página **controle de acesso** é aberta. Nessa página, você pode examinar o nível de acesso de alguém ou adicionar uma nova atribuição de função. Ao adicionar uma atribuição de função aqui, você está concedendo permissões ao painel.

> [!NOTE]
> Os blocos são exibições representativas de recursos em sua organização. O acesso a recursos é gerenciado por meio da atribuição de controle de acesso baseado em função e as permissões são herdadas da assinatura para o recurso. Conceder acesso a um painel não atribui automaticamente permissões aos recursos mostrados no painel. Para obter mais informações sobre permissões para painéis compartilhados e controle de acesso baseado em função para recursos, consulte [compartilhar painéis com o controle de acesso baseado em função](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Abrir um painel compartilhado

Para localizar e abrir um painel compartilhado, siga estas etapas:

1. Selecione a lista suspensa ao lado do nome do painel.
1. Selecione na lista exibida de painéis ou **procure todos os painéis** se o painel que você deseja abrir não estiver listado.

   ![Captura de tela do menu de seleção do painel](./media/azure-portal-dashboards/dashboard-browse.png)

3. No campo **tipo** , selecione **painéis compartilhados**.
1. Selecione uma ou mais assinaturas. Você também pode inserir texto para filtrar os painéis por nome.
1. Selecione um painel na lista de painéis compartilhados.

## <a name="delete-a-dashboard"></a>Eliminar um dashboard

Para excluir permanentemente um painel particular ou compartilhado, siga estas etapas:

1. Selecione o painel que você deseja excluir na lista suspensa ao lado do nome do painel.
1. Selecione ![ícone Excluir](./media/azure-portal-dashboards/dashboard-delete-icon.png) **excluir** do cabeçalho da página.
1. Para um painel particular, selecione **OK** na caixa de diálogo de confirmação para remover o painel. Para um painel compartilhado, na caixa de diálogo de confirmação, marque a caixa de seleção para confirmar se o painel publicado não será mais visível para outras pessoas. Em seguida, selecione **OK**.

   ![Captura de tela da confirmação de exclusão](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passos seguintes

* [Compartilhar painéis com o controle de acesso baseado em função](azure-portal-dashboard-share-access.md)
* [Criar programaticamente painéis do Azure](azure-portal-dashboards-create-programmatically.md)
