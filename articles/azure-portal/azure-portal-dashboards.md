---
title: Criar e partilhar dashboards de portais do Azure | Documentos da Microsoft
description: Este artigo descreve como criar, personalizar, publicar e partilhar dashboards no portal do Azure.
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
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537215"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e partilhar dashboards no portal do Azure

Dashboards fornecem uma forma de criar uma vista focada e organizada no portal do Azure dos seus recursos na cloud. Utilize dashboards como uma área de trabalho onde pode rapidamente iniciar tarefas das operações diárias e monitorizar os recursos.  Crie dashboards personalizados com base em projetos, tarefas ou funções de utilizador, por exemplo.  O portal do Azure fornece um dashboard predefinido, como um ponto de partida. Pode editar o dashboard predefinido, crie e personalize dashboards adicionais e publicar e partilhar dashboards para que fiquem disponíveis para outros utilizadores. Este artigo descreve como criar um novo dashboard, personalizar a interface e publicar e partilhar dashboards.

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard

Neste exemplo, vamos criar um dashboard novo, privado e atribuir um nome. Siga estes passos para começar a utilizar:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Dashboard** da seção superior da barra lateral esquerda. Já que sua exibição padrão pode ser definida para o dashboard.
1. Selecione **+ novo dashboard**.

    ![Captura de ecrã do dashboard predefinido](./media/azure-portal-dashboards/dashboard-new.png)

4. Esta ação abre o **Galeria de mosaicos**, do qual irá selecionar mosaicos e uma grade vazia onde irá dispor os mosaicos.

    ![Captura de ecrã da Galeria de mosaicos e grelha vazia](./media/azure-portal-dashboards/dashboard-name.png)

5. Selecione o **meu painel** texto no dashboard de etiqueta e introduza um nome que irá ajudá-lo a identificar facilmente o dashboard personalizado.
1. Selecione **personalização concluída** no cabeçalho da página para sair do modo de edição.

A vista do dashboard mostra agora o seu dashboard vazia. Selecione o menu pendente junto ao nome do dashboard para ver os dashboards disponíveis para – a lista pode incluir os dashboards que outros usuários tenham criados e partilhados.

## <a name="edit-a-dashboard"></a>Editar um dashboard

Agora, vamos editar dashboard para adicionar, redimensionar e organizar os mosaicos que representam os recursos do Azure.

### <a name="add-tiles"></a>Adicionar mosaicos

Para adicionar mosaicos a um dashboard, siga estes passos:
1. Selecione ![ícone editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **editar** do cabeçalho da página.

    ![Captura de ecrã do dashboard, realce de edição](./media/azure-portal-dashboards/dashboard-edit.png)

2. Procurar os **Galeria de mosaicos** ou utilize o campo de pesquisa para encontrar o mosaico que pretende.
1. Selecione **adicionar** para adicionar automaticamente o mosaico no dashboard com um tamanho predefinido e localização. Ou, arraste o mosaico para a grade e coloque-o onde quiser.

Várias páginas de recursos (também conhecido como "painéis") incluem um ícone de alfinete na barra de comandos. Se selecionar o ícone, um mosaico que representa a página de origem está afixado ao dashboard atualmente ativo. Esse método é uma forma alternativa de adicionar mosaicos ao dashboard.

![Captura de ecrã da barra de comandos de página com o ícone de pin](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Se trabalha com mais do que uma organização, adicione a **identidade da organização** mosaico ao dashboard para mostrar claramente organização da qual os recursos pertencem a.
>
>
### <a name="resize-or-rearrange-tiles"></a>Redimensionar ou reorganizar os mosaicos
Para alterar o tamanho de um mosaico ou reorganizar os mosaicos num dashboard, siga estes passos:

1. Selecione ![ícone editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **editar** do cabeçalho da página.
1. Selecione o menu de contexto no canto superior direito de um mosaico. Em seguida, escolha um tamanho de bloco. Mosaicos que suportam qualquer tamanho incluem também um "identificador" no canto inferior direito que permite arrastar o mosaico para o tamanho pretendido.

   ![Captura de ecrã do dashboard com o menu de tamanho de mosaico aberto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Selecione um mosaico e arraste-o para uma nova localização na grade para organizar o seu dashboard.

### <a name="additional-tile-configuration"></a>Configuração de mosaico adicionais

Alguns mosaicos, poderão ter mais de configuração para mostrar as informações que pretende. Por exemplo, o **gráfico de métricas** tem de ser configurados para exibir uma métrica de mosaico **Azure Monitor**. Também pode personalizar os dados de mosaico para substituir definições de hora padrão do dashboard.

Qualquer mosaico que tem de ser configurado apresenta um **configurar mosaico** faixa até que personalizar o mosaico. Selecione essa faixa e, em seguida, a configuração necessária.

![Captura de ecrã do mosaico que requer configuração](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Um mosaico markdown permite-lhe apresentar conteúdo personalizado, estático no seu dashboard. Isto pode ser instruções básicas, uma imagem, um conjunto de hiperlinks ou até mesmo informações de contacto. Para obter mais informações sobre a utilização de um mosaico de markdown, veja [utilize um mosaico markdown personalizada](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personalizar os dados de mosaico

Dados no dashboard mostram automaticamente a atividade para as últimas 24 horas. Para mostrar um intervalo de tempo diferente para apenas este mosaico, siga estes passos:

1. Selecione o ![ícone de filtro](./media/azure-portal-dashboards/dashboard-filter.png) ícone de filtro no canto superior esquerdo do mosaico ou selecione **dados do mosaico personalizar** no menu de contexto.

   ![Captura de ecrã do menu de contexto do mosaico](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Selecione a caixa de verificação **substituir as definições de hora do dashboard ao nível do mosaico**.

   ![Captura de ecrã da caixa de diálogo para configurar as definições de hora do mosaico](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Escolha o intervalo de tempo para mostrar para este mosaico. Pode escolher entre os últimos 30 minutos para nos últimos 30 dias ou definir um intervalo personalizado.
1. Escolha a granularidade de tempo para apresentar. Pode mostrar em qualquer lugar de incrementos de um minuto para um mês.
1. Selecione **Aplicar**.

## <a name="delete-a-tile"></a>Eliminar um mosaico

Para remover um mosaico a partir de um dashboard, siga estes passos:

* Selecione o menu de contexto no canto superior direito do mosaico, em seguida, selecione **remover do dashboard**. Ou,
* Selecione ![ícone editar](./media/azure-portal-dashboards/dashboard-edit-icon.png) **editar** para entrar no modo de personalização. Coloque o cursor no canto superior direito do mosaico, em seguida, selecione o ![ícone Eliminar](./media/azure-portal-dashboards/dashboard-delete-icon.png) eliminar ícone para remover o mosaico de dashboard.

   ![Captura de ecrã que mostra como remover o mosaico do dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonar um dashboard

Para utilizar um dashboard existente como modelo para um novo dashboard, siga estes passos:

1. Certifique-se de que a vista do dashboard está a mostrar o dashboard que pretende copiar.
1. No cabeçalho da página, selecione ![ícone de clone](./media/azure-portal-dashboards/dashboard-clone.png) **Clone**.
1. Uma cópia do dashboard, com o nome "clonar dos *seu nome de dashboard*" é aberto no modo de edição. Utilize os passos anteriores neste artigo para mudar o nome e personalizar o dashboard.

## <a name="publish-and-share-a-dashboard"></a>Publicar e partilhar um dashboard

Quando cria um dashboard, é privado por predefinição, o que significa que é o único que o pode ver. Para disponibilizar a outros dashboards, pode partilhá-los com outros utilizadores. Em primeiro lugar, terá de publicar o dashboard como um recurso do Azure. Para publicar e partilhar um dashboard personalizado, siga estes passos:

1. Selecione ![ícone partilhar](./media/azure-portal-dashboards/dashboard-share-icon.png) **partilhar** do cabeçalho da página. O **partilha + de controle de acesso** formulário seja apresentado.
1. Certifique-se de que o nome do dashboard correto é mostrado.
1. Selecione um **nome da subscrição**. Os utilizadores com acesso à subscrição podem utilizar o dashboard partilhado. Acesso aos recursos representado pelos mosaicos individuais é determinado pelo controlo de acesso baseado em funções do Azure.
1. Selecione a caixa de verificação para publicar este dashboard para o grupo de recursos 'dashboards' para a subscrição selecionada. Ou, desmarque a caixa de verificação e optar por publicar a um grupo de recursos existente em vez disso.
1. Escolha uma localização para o recurso de dashboard. Recomendamos que localize o dashboard com outros recursos. Nota: Se escolher entre grupos de recursos existentes, o dashboard é automaticamente localizado com o grupo de recursos.
1. Selecione **Publicar**.

   ![Captura de ecrã da caixa de diálogo publicação do dashboard](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Definir o controlo de acesso num dashboard partilhado

Depois do dashboard é publicado, geri quem tem acesso ao dashboard ao seguir estes passos:

1. Na **partilha + de controle de acesso** painel, selecione **gerir utilizadores**.

   ![Caixa de diálogo de controlar a captura de ecrã da partilha de dashboards + acesso](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. O **controlo de acesso** é aberta a página. Nesta página, pode rever o nível de acesso para alguém ou adicionar uma nova atribuição de função. Quando adiciona uma atribuição de função aqui, está concedendo permissões ao dashboard.

> [!NOTE]
> Blocos são vistas representativas de recursos na sua organização. Acesso a recursos é gerenciado por meio de atribuição de controlo de acesso baseado em funções e permissões são herdadas da subscrição para baixo para o recurso. Que lhe dá acesso a um dashboard não atribui automaticamente permissões para os recursos mostrados no dashboard. Para obter mais informações sobre as permissões de controlo de acesso baseado em funções para recursos e dashboards partilhados, consulte [partilhar dashboards com controlo de acesso baseado em funções](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Abra um dashboard partilhado

Para encontrar e abrir um dashboard partilhado, siga estes passos:

1. Selecione a menu pendente junto ao nome do dashboard.
1. Selecione entre a lista apresentada de dashboards ou **procurar todos os dashboards** se não estiver listado o dashboard que pretende abrir.

   ![Captura de ecrã do menu de seleção do dashboard](./media/azure-portal-dashboards/dashboard-browse.png)

3. Na **tipo** campo, selecione **dashboards partilhados**.
1. Selecione uma ou mais subscrições. Também pode introduzir texto para filtrar dashboards por nome.
1. Selecione um dashboard a partir da lista de dashboards partilhados.

## <a name="delete-a-dashboard"></a>Eliminar um dashboard

Para eliminar permanentemente um dashboard partilhado ou privado, siga estes passos:

1. Selecione o dashboard que pretende eliminar a partir da lista pendente junto ao nome do dashboard.
1. Selecione ![ícone Eliminar](./media/azure-portal-dashboards/dashboard-delete-icon.png) **eliminar** do cabeçalho da página.
1. Para um dashboard privado, selecione **OK** da caixa de diálogo de confirmação para remover o dashboard. Para um dashboard partilhado, na caixa de diálogo de confirmação, selecione a caixa de verificação para confirmar que o dashboard publicado deixará de estar visto por outros utilizadores. Em seguida, selecione **OK**.

   ![Captura de ecrã de confirmação de eliminação](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Passos Seguintes

* [Partilhar dashboards com controlo de acesso baseado em funções](azure-portal-dashboard-share-access.md)
* [Criar programaticamente dashboards do Azure](azure-portal-dashboards-create-programmatically.md)
