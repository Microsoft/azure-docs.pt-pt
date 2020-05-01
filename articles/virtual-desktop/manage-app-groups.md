---
title: Gerir grupos de aplicativos para o portal Windows Virtual Desktop - Azure
description: Como gerir os grupos de aplicações do Windows Virtual Desktop com o portal Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612190"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Tutorial: Gerir grupos de aplicações com o portal Azure

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O grupo de aplicações predefinido criado para um novo pool de anfitriões do Windows Virtual Desktop também publica todo o ambiente de trabalho. Além disso, pode criar um ou mais grupos de aplicações RemoteApp para o pool anfitrião. Siga este tutorial para criar um grupo de aplicações RemoteApp e publique aplicações individuais de menu Start.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo RemoteApp.
> * Conceder acesso a programas RemoteApp.

## <a name="create-a-remoteapp-group"></a>Criar um grupo RemoteApp

Se já criou um anfitrião e vMs de anfitrião de sessão usando o portal Azure ou PowerShell, pode adicionar grupos de aplicações do portal Azure com o seguinte processo:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).

2.  Procure e selecione **Windows Virtual Desktop**.

3.  Selecione **Grupos de Aplicação** no menu do lado esquerdo da página e, em seguida, selecione **+ Adicionar**.

4. No separador **Basics,** selecione o grupo de subscrição e o grupo de recursos para o quais pretende criar o grupo de aplicações. Também pode optar por criar um novo grupo de recursos em vez de selecionar um existente.

5. Selecione o pool anfitrião que será associado ao grupo de aplicação a partir do menu suspenso ao lado da **piscina anfitrião**.

    >[!NOTE]
    >Deve selecionar a piscina de anfitriões associada ao grupo de aplicação. Os grupos de aplicações têm apps ou desktops que são servidos a partir de um anfitrião de sessão e os anfitriões de sessão fazem parte das piscinas de anfitriões. O grupo de aplicações precisa de ser associado a uma piscina de anfitriões durante a criação.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem do separador Basics no portal Azure.](media/basics-tab.png)

6. Se quiser adicionar grupos de aplicações à sua piscina anfitriã, selecione **piscinas host** no menu do lado esquerdo do ecrã.
   
    Em seguida, selecione o nome da piscina anfitriã a que pretende adicionar grupos de aplicações.
   
    Depois disso, selecione **grupos de aplicação** do menu do lado esquerdo do ecrã e, em seguida, selecione **+Adicionar**.

    Por fim, selecione o grupo de subscrição e o grupo de recursos em que pretende criar o grupo de aplicações. Pode selecionar o nome de um grupo de recursos existente a partir do menu suspenso ou selecionar **Criar novo** para fazer um novo.

      >[!NOTE]
      >Quando você adiciona grupos de aplicação ao seu pool anfitrião, o pool de anfitriões que está relacionado com o grupo de aplicações já está selecionado porque você navegou a partir dele.
      > 
      > [!div class="mx-imgBorder"]
      >![Uma imagem do separador Basics com a piscina do hospedeiro pré-selecionada.](media/host-pool-selected.png)

7. Selecione **RemoteApp** no tipo de grupo de aplicação e, em seguida, introduza um nome para o seu RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Uma imagem dos campos do tipo de grupo aplicação. "RemoteApp" está em destaque.](media/remoteapp-button.png)

8.  Selecione o separador **Atribuições.**

9.  Para publicar utilizadores individuais ou grupos de utilizadores no grupo de aplicações, **selecione +Add Azure AD utilizadores ou grupos de utilizadores**.

10.  Selecione o número de utilizadores a que pretende adicionar as aplicações. Pode selecionar grupos de utilizadores e utilizadores individuais ou múltiplos.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do menu de seleção do utilizador.](media/select-users.png)

11.  Selecione **Selecionar**.

12.  Selecione o separador **Aplicações** e, em seguida, selecione **+Adicionar aplicações**.

13.  Para adicionar uma aplicação a partir do menu inicial: 

      - Vá à fonte de **aplicação** e selecione **menu Iniciar** a partir do menu suspenso. Em seguida, vá à **Aplicação** e escolha a aplicação a partir do menu suspenso.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do ecrã de aplicação adicionar com o menu Iniciar selecionado.](media/add-app-start.png)

      - No **nome Display,** introduza o nome da aplicação que será mostrada ao utilizador no seu cliente.

      - Deixe as outras opções como estão e selecione **Guardar**.

14. Para adicionar uma aplicação a partir de um caminho de ficheiro específico:

      - Vá à fonte de **aplicação** e selecione **o caminho** de Arquivo a partir do menu suspenso.

      - Insira o caminho para a candidatura no anfitrião da sessão, registado na piscina de anfitriões associada.

      - Introduza os detalhes da aplicação no nome da **aplicação,** nome do **ecrã,** **caminho do ícone**e nos campos de índice do **Ícone.**

      - Selecione **Guardar**.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página de aplicação de adição com o caminho de ficheiro selecionado.](media/add-app-file.png)

     Repita este processo para cada aplicação que pretende adicionar ao grupo de aplicações.

15.  Em seguida, selecione o separador **Workspace.**

16.  Se quiser registar o grupo de aplicações num espaço de trabalho, vá ao Grupo de **Aplicações Register** e selecione **Sim**. Se preferir registar o grupo de aplicações mais tarde, selecione **No**.

17.  Se selecionar **Sim,** pode selecionar um espaço de trabalho existente para registar o seu grupo de aplicações.
       
       >[!NOTE]
       >Só pode registar o grupo de aplicações para espaços de trabalho criados no mesmo local que a piscina anfitriã. E também. se já registou outro grupo de aplicações do mesmo grupo de anfitriões que o seu novo grupo de aplicações para um espaço de trabalho, será selecionado e não poderá editá-lo. Todos os grupos de aplicações de uma piscina de anfitriões devem estar registados no mesmo espaço de trabalho.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página do grupo de aplicação de registo para um espaço de trabalho já existente. A piscina de anfitriões está pré-selecionada.](media/register-existing.png)

18. Opcionalmente, se quiser criar tags para facilitar a organização do seu espaço de trabalho, selecione o separador **Tags** e introduza os seus nomes de etiquetas.

19. Quando terminar, selecione o **separador Review + criar.**

20. Aguarde um pouco para que o processo de validação esteja concluído. Quando estiver feito, selecione **Criar** para implementar o seu grupo de aplicações.

O processo de implantação fará as seguintes coisas por si:

- Crie o grupo de aplicações RemoteApp.
- Adicione as suas aplicações selecionadas ao grupo de aplicações.
- Publique o grupo de aplicações publicado para utilizadores e grupos de utilizadores selecionados.
- Registe o grupo de aplicações, caso opte por fazê-lo.
- Crie um link para um modelo de Gestor de Recursos Azure com base na sua configuração que pode descarregar e guardar para mais tarde.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um grupo de aplicações, povoá-lo com programas RemoteApp e atribuir utilizadores ao grupo de aplicações. Para aprender a criar uma piscina de anfitriões de validação, consulte o seguinte tutorial. Você pode usar um pool anfitrião de validação para monitorizar atualizações de serviço antes de as lançar para o seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um conjunto de anfitriões para validar as atualizações de serviço](./create-validation-host-pool.md)
