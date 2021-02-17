---
title: Tutorial - Gerir o acesso aos recursos na gestão de direitos da Ad Azure
description: Tutorial passo a passo para como criar o seu primeiro pacote de acesso utilizando o portal Azure na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adbc653c8b698cb7b439b54b0a77d2b8cd4042a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547405"
---
# <a name="tutorial-manage-access-to-resources-in-azure-ad-entitlement-management"></a>Tutorial: Gerir o acesso aos recursos na gestão de direitos da AD Azure

Gerir o acesso a todos os recursos de que os colaboradores precisam, como grupos, aplicações e sites, é uma função importante para as organizações. Pretende-se conceder aos colaboradores o nível de acesso adequado de que precisam para serem produtivos e remover o seu acesso quando já não é necessário.

Neste tutorial, trabalha para o Woodgrove Bank como administrador de TI. Foi-lhe pedido que criasse um pacote de recursos para uma campanha de marketing que os utilizadores internos podem usar para solicitar o autosserviço. Os pedidos não requerem aprovação e o acesso do utilizador expira após 30 dias. Para este tutorial, os recursos da campanha de marketing são apenas membros de um único grupo, mas pode ser uma coleção de grupos, aplicações ou sites SharePoint Online.

![Diagrama que mostra a visão geral do cenário.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como recurso
> * Permitir que um utilizador no seu diretório solicite acesso
> * Demonstrar como um utilizador interno pode solicitar o pacote de acesso

Para uma demonstração passo a passo do processo de implementação da gestão de direitos do Azure Ative Directory, incluindo a criação do seu primeiro pacote de acesso, veja o seguinte vídeo:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de direitos AD da Azure, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença E5 do Enterprise Mobility + Security (EMS)

Para mais informações, consulte [os requisitos da Licença.](entitlement-management-overview.md#license-requirements)

## <a name="step-1-set-up-users-and-group"></a>Passo 1: Criar utilizadores e grupo

Um diretório de recursos tem um ou mais recursos para partilhar. Neste passo, você cria um grupo chamado **recursos de marketing** no diretório do Woodgrove Bank que é o recurso alvo para a gestão de direitos. Também criou um solicitador interno.

**Papel pré-requisito:** Administrador global ou administrador de utilizadores

![Criar utilizadores e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global ou administrador do Utilizador.  

1. Na navegação à esquerda, clique no **Diretório Ativo Azure**.

1. Criar ou configurar os dois utilizadores seguintes. Pode usar estes nomes ou nomes diferentes. **ADmin1** pode ser o utilizador em que está atualmente inscrito como.

    | Name | Função de diretório |
    | --- | --- |
    | **Administrador1** | Administrador global<br/>-ou-<br/>Administrador de utilizadores |
    | **Requestor1** | User |

1. Criar um grupo de segurança Azure AD chamado **Recursos de Marketing** com um tipo de membro de **Atribuídos**.

    Este grupo será o recurso-alvo para a gestão de direitos. O grupo deve estar vazio de membros para começar.

## <a name="step-2-create-an-access-package"></a>Passo 2: Criar um pacote de acesso

Um pacote de *acesso* é um pacote de recursos que uma equipa ou projeto precisa e é governado com políticas. Os pacotes de acesso são definidos em contentores *chamados catálogos.* Neste passo, você cria um pacote de acesso **à Campanha de Marketing** no catálogo **geral.**

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal Azure, na navegação à esquerda, clique no **Diretório Ativo Azure**.

2. No menu à esquerda, clique em **Governança de Identidade**

3. No menu esquerdo, clique em **pacotes de Acesso**.  Se vir **Access negado,** certifique-se de que uma licença Azure AD Premium P2 está presente no seu diretório.

4. Clique **em Novo pacote de acesso**.

    ![Gestão de direitos no portal Azure](./media/entitlement-management-shared/access-packages-list.png)

5. No **separador Básicos,** escreva o pacote de acesso **da campanha de marketing** e descrição Acesso aos recursos para a **campanha.**

6. Deixe a lista de entrega do **catálogo** definida para **o General**.

    ![Novo pacote de acesso - Separador básico](./media/entitlement-management-access-package-first/basics.png)

7. Clique **em seguida** para abrir o separador **Funções de Recurso.**

    Neste separador, selecione os recursos e a função de recurso a incluir no pacote de acesso.

8. Clique **em Grupos e Equipas.**

9. No painel de grupos Select, encontre e selecione o grupo **de recursos de Marketing** que criou anteriormente.

     Por defeito, vê grupos dentro do catálogo geral. Quando selecionar um grupo fora do catálogo Geral, que pode ver se verifica a caixa de verificação **See,** será adicionado ao catálogo Geral.

    ![Screenshot que mostra o separador "Novo pacote de acesso - funções de recursos" e a janela "Select groups".](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Clique **em Selecionar** para adicionar o grupo à lista.

11. Na lista de drop-down **role,** selecione **Member**.

    ![Novo pacote de acesso - Separador de funções de recursos](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!IMPORTANT]
    >Os grupos atribuíveis a funções adicionados a um pacote de acesso serão indicados utilizando o sub tipo **atribuível às funções**. Consulte a [Criação de um grupo atribuível a funções](../roles/groups-create-eligible.md) no Azure Ative Directory para obter mais detalhes sobre grupos atribuíveis às funções AZure AD. Tenha em mente que uma vez que um grupo atribuível a funções esteja presente num catálogo de pacotes de acesso, os utilizadores administrativos que sejam capazes de gerir na gestão de direitos, incluindo administradores globais, administradores de utilizadores e proprietários de catálogos do catálogo, poderão controlar os pacotes de acesso no catálogo, permitindo-lhes escolher quem pode ser adicionado a esses grupos. Se não vir um grupo atribuível a um papel que pretenda adicionar ou não conseguir adicioná-lo, certifique-se de que tem o papel de AD Azure e o papel de gestão de direitos necessários para executar esta operação. Pode ser necessário perguntar a alguém com as funções necessárias que adicione o recurso ao seu catálogo. Para obter mais informações, consulte [as funções necessárias para adicionar recursos a um catálogo.](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

    >[!NOTE]
    > Ao utilizar [grupos dinâmicos,](../enterprise-users/groups-create-rule.md) não verá outras funções disponíveis para além do proprietário. Esta ação é propositada.
    > ![Scenario overview (Descrição geral do cenário)](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. Clique **ao lado** para abrir o separador **Pedidos.**

    Neste separador, cria-se uma política de pedidos. Uma *política* define as regras ou guarda-costas para aceder a um pacote de acesso. Cria uma política que permite a um utilizador específico no diretório de recursos solicitar este pacote de acesso.

13. Nos **Utilizadores que podem solicitar a** secção de acesso, clique **em Para utilizadores no seu diretório** e, em seguida, clique em **utilizadores e grupos específicos**.

    ![Novo pacote de acesso - Separador de Pedidos](./media/entitlement-management-access-package-first/requests.png)

14. Clique **em Adicionar utilizadores e grupos**.

15. No painel de grupos Select, selecione o utilizador **Requestor1** que criou anteriormente.

    ![Novo pacote de acesso - Separador de Pedidos - Selecione utilizadores e grupos](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Clique em **Selecionar**.

17. Percorra para baixo para as secções **de aprovação** e ative as secções **de pedidos.**

18. Deixar **Requerer a aprovação** definida para **o nº**.

19. Para **ativar pedidos**, clique em **Sim** para permitir que este pacote de acesso seja solicitado assim que for criado.

    ![Novo pacote de acesso - Separador de Pedidos - Aprovação e Ativação de pedidos](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Clique **ao lado** para abrir o **separador Ciclo de Vida.**

21. Na secção **Expiração,** definir **as atribuições de pacotes** de acesso expiram para **o Número de dias**.

22. **As atribuições de definição expiram após** **30** dias.

    ![Novo pacote de acesso - Separador de ciclo de vida](./media/entitlement-management-access-package-first/lifecycle.png)

23. Clique em **Seguida** para abrir o **separador 'Rever + Criar'.**

    ![Novo pacote de acesso - Rever + Criar separador](./media/entitlement-management-access-package-first/review-create.png)

    Após alguns momentos, deverá ver uma notificação de que o pacote de acesso foi criado com sucesso.

24. No menu esquerdo do pacote de acesso à Campanha de Marketing, clique **em Visão Geral.**

25. Copie o **link do portal My Access**.

    Vais usar este link para o próximo passo.

    ![Visão geral do pacote de acesso - O link do portal Do Meu Acesso](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Passo 3: Solicitar acesso

Neste passo, executa as etapas como **solicitador interno** e solicita o acesso ao pacote de acesso. Os solicitadores submetem os seus pedidos através de um site chamado portal My Access. O portal My Access permite aos solicitadores submeterem pedidos de pacotes de acesso, verem os pacotes de acesso a que já têm acesso e visualizarem o histórico de pedidos.

**Papel pré-requisito:** Solicitador interno

1. Assine fora do portal Azure.

1. Numa nova janela do navegador, navegue para o link do portal My Access copiado no passo anterior.

1. Inscreva-se no portal 'O Acesso' como **Requestor1**.

    Devia ver o pacote de acesso **à Campanha de Marketing.**

1. Se necessário, na coluna **Descrição,** clique na seta para ver detalhes sobre o pacote de acesso.

    ![Meu portal de acesso - Pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de verificação para selecionar o pacote.

1. Clique **em Pedir acesso** para abrir o painel de acesso do Pedido.

    ![O meu portal de acesso - Solicite o botão de acesso](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Na caixa **de justificação do Negócio,** escreva a justificação **que estou a trabalhar na nova campanha de marketing.**

    ![O meu portal de Acesso - Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique **em Submeter.**

1. No menu esquerdo, clique em **Pedir histórico** para verificar se o seu pedido foi submetido.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Passo 4: Validar que o acesso foi atribuído

Neste passo, confirma-se que o **solicitador interno** foi designado o pacote de acesso e que agora são membros do grupo de **recursos de Marketing.**

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. Assine fora do portal 'O Acesso'.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como **Admin1**.

1. Clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso**.

1. Encontre e clique no pacote de acesso **à campanha de marketing.**

1. No menu esquerdo, clique em **Pedidos**.

    Deverá consultar o Requestor1 e a política inicial com um estatuto de **Entrega**.

1. Clique no pedido para ver os detalhes do pedido.

    ![Pacote de acesso - Solicitar detalhes](./media/entitlement-management-access-package-first/request-details.png)

1. Na navegação à esquerda, clique no **Diretório Ativo Azure**.

1. Clique **em Grupos** e abra o grupo **de recursos de Marketing.**

1. Clique **em Membros**.

    Devia ver **o Requestor1** listado como membro.

    ![Membros de recursos de marketing](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Passo 5: Limpar recursos

Neste passo, remove as alterações que esou e elimina o pacote de acesso **à Campanha de Marketing.**

**Papel pré-requisito:**  Administrador global ou administrador de utilizadores

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. Abra o pacote de acesso **à Campanha de Marketing.**

1. Clique **em Atribuições**.

1. Para **Requestor1,** clique na elipse **(...)** e clique em **Remover o acesso**. Na mensagem que aparece, clique em **Sim.**

    Após alguns momentos, o estado passará de Entregue a Expirado.

1. Clique **em funções de recurso.**

1. Para **os recursos de Marketing,** clique na elipse **(...**) e, em seguida, clique em **Remover a função de recurso**. Na mensagem que aparece, clique em **Sim.**

1. Abra a lista de pacotes de acesso.

1. Para **campanha de marketing,** clique na elipse **(...**) e clique em **Apagar**. Na mensagem que aparece, clique em **Sim.**

1. No Azure Ative Directory, elimine quaisquer utilizadores que tenha criado, como **o Requestor1** e **o Admin1**.

1. Eliminar o grupo **de recursos de Marketing.**

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para conhecer os passos comuns do cenário na gestão de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
