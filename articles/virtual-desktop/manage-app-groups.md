---
title: Gerir grupos de aplicações para o portal de desktop virtual do Windows - Azure
description: Como gerir os grupos de aplicações virtual do Windows Desktop com o portal Azure.
author: Heidilohr
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6baada4c3b376b936f693d45e39e5887e77ef54b
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010094"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Tutorial: Gerir grupos de aplicações com o portal Azure

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

O grupo de aplicações padrão criado para um novo conjunto de anfitriões do Windows Virtual Desktop também publica o ambiente de trabalho completo. Além disso, pode criar um ou mais grupos de aplicações RemoteApp para o pool anfitrião. Siga este tutorial para criar um grupo de aplicações RemoteApp e publicar aplicações individuais do menu Start.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo RemoteApp.
> * Conceder acesso a programas RemoteApp.

## <a name="create-a-remoteapp-group"></a>Criar um grupo RemoteApp

Se já criou um VM de anfitrião de pool e sessão utilizando o portal Azure ou PowerShell, pode adicionar grupos de aplicações a partir do portal Azure com o seguinte processo:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).

2.  Procure e selecione **Windows Virtual Desktop**.

3.  Selecione **Grupos de Aplicações** no menu no lado esquerdo da página e, em seguida, selecione **+ Adicionar**.

4. No **separador Basics,** selecione o grupo de subscrição e o grupo de recursos para o quais pretende criar o grupo de aplicações. Também pode optar por criar um novo grupo de recursos em vez de selecionar um existente.

5. Selecione a piscina anfitriã que será associada ao grupo de aplicação a partir do menu suspenso ao lado **da piscina Host**.

    >[!NOTE]
    >Tem de selecionar a piscina de anfitriões associada ao grupo de aplicação. Os grupos de aplicações têm apps ou desktops que são servidos a partir de um anfitrião de sessão e os anfitriões de sessão fazem parte de piscinas hospedeiras. O grupo de aplicações precisa de ser associado a uma piscina de anfitriões durante a criação.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem do separador Basics no portal Azure.](media/basics-tab.png)

6. Se pretender adicionar grupos de aplicação à sua piscina de anfitrião, selecione **as piscinas host** no menu do lado esquerdo do ecrã.

    Em seguida, selecione o nome da piscina de anfitrião a que pretende adicionar grupos de aplicação.

    Depois disso, selecione grupos de **aplicação** do menu no lado esquerdo do ecrã e, em seguida, selecione **+Adicionar**.

    Por fim, selecione o grupo de subscrição e o grupo de recursos em que pretende criar o grupo de aplicações. Pode selecionar o nome de um grupo de recursos existente a partir do menu suspenso ou selecionar **Criar novo** para fazer um novo.

      >[!NOTE]
      >Quando adiciona grupos de aplicação à sua piscina de anfitriões, a piscina anfitriã que está relacionada com o grupo de aplicações já está selecionada porque navegou a partir dele.
      >
      > [!div class="mx-imgBorder"]
      >![Uma imagem do separador Basics com a piscina hospedeira pré-selecionado.](media/host-pool-selected.png)

7. Selecione **RemoteApp** sob o tipo de grupo aplicação e, em seguida, introduza um nome para o seu RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Uma imagem dos campos do tipo grupo Application. "RemoteApp" está em destaque.](media/remoteapp-button.png)

8.  Selecione o **separador Atribuições.**

9.  Para publicar utilizadores individuais ou grupos de utilizadores para o grupo de aplicações, **selecione +Add Azure AD ou grupos de utilizadores**.

10.  Selecione o número de utilizadores a que pretende adicionar as aplicações. Pode selecionar utilizadores únicos ou múltiplos e grupos de utilizadores.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do menu de seleção do utilizador.](media/select-users.png)

11.  Selecione **Selecionar**.

12.  Selecione o separador **Aplicações** e, em seguida, selecione **+Adicionar aplicações**.

13.  Para adicionar uma aplicação no menu inicial:

      - Aceda à **fonte de aplicação** e selecione **o menu Iniciar** a partir do menu suspenso. Em seguida, vá à **Aplicação** e escolha a aplicação no menu suspenso.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do ecrã de aplicação adicionar com o menu Iniciar selecionado.](media/add-app-start.png)

      - No **nome Display,** insira o nome da aplicação que será mostrada ao utilizador no seu cliente.

      - Deixe as outras opções como está e **selecione Guardar**.

14. Para adicionar uma aplicação a partir de um caminho de arquivo específico:

      - Aceda à **fonte de aplicação** e selecione **o caminho** de arquivo a partir do menu suspenso.

      - Insira o caminho para a inscrição no anfitrião da sessão, registrado com a piscina anfitriã associada.

      - Introduza os detalhes da aplicação no nome da **aplicação,** **nome de exibição,** **caminho do ícone**e índice de **ícone.**

      - Selecione **Guardar**.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página de aplicação adicionar com o caminho do ficheiro selecionado.](media/add-app-file.png)

     Repita este processo para cada aplicação que pretende adicionar ao grupo de aplicações.

15.  Em seguida, selecione o **separador Espaço de Trabalho.**

16.  Se pretender registar o grupo de aplicações num espaço de trabalho, vá ao **grupo de aplicações Register** e selecione **Sim**. Se preferir registar o grupo de aplicações mais tarde, selecione **Nº**.

17.  Se selecionar **Sim,** pode selecionar um espaço de trabalho existente para registar o seu grupo de aplicações.

       >[!NOTE]
       >Só é possível registar o grupo de aplicações nos espaços de trabalho criados no mesmo local que a piscina anfitriã. E também. se já registou outro grupo de aplicações do mesmo grupo de anfitriões que o seu novo grupo de aplicações para um espaço de trabalho, será selecionado e não poderá editá-lo. Todos os grupos de aplicações de uma piscina de anfitriões devem estar registados no mesmo espaço de trabalho.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página do grupo de inscrição para um espaço de trabalho já existente. A piscina de anfitriões está pré-selecionado.](media/register-existing.png)

18. Opcionalmente, se pretender criar tags para facilitar a organização do seu espaço de trabalho, selecione o separador Tags e introduza os seus **nomes** de identificação.

19. Quando terminar, selecione o **separador 'Rever +' para criar.**

20. Aguarde um pouco para que o processo de validação esteja concluído. Quando estiver feito, selecione **Criar** para implementar o seu grupo de aplicações.

O processo de implantação fará as seguintes coisas por si:

- Crie o grupo de aplicações RemoteApp.
- Adicione as suas aplicações selecionadas ao grupo de aplicações.
- Publique o grupo de aplicações publicado aos utilizadores e grupos de utilizadores que selecionou.
- Registe o grupo de aplicações, se optar por fazê-lo.
- Crie um link para um modelo de Gestor de Recursos Azure com base na sua configuração que pode descarregar e guardar para mais tarde.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um grupo de aplicações, povoá-lo com programas RemoteApp e atribuir utilizadores ao grupo de aplicações. Para aprender a criar uma piscina de anfitriões de validação, consulte o seguinte tutorial. Pode utilizar um pool de anfitriões de validação para monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um conjunto de anfitriões para validar as atualizações de serviço](./create-validation-host-pool.md)
