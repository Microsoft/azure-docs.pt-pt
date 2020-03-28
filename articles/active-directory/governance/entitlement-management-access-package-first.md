---
title: Tutorial - Criar pacote de acesso - Gestão de direitos da Azure AD
description: Tutorial passo a passo para como criar o seu primeiro pacote de acesso na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4feeb83acc3842874e7a2e4bbd32dacabcc00d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75422662"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Tutorial: Crie o seu primeiro pacote de acesso na gestão de direitos da AD Azure

Gerir o acesso a todos os recursos que os colaboradores precisam, como grupos, aplicações e sites, é uma função importante para as organizações. Pretende-se conceder aos colaboradores o nível de acesso adequado, precisam de ser produtivos e remover o seu acesso quando já não é necessário.

Neste tutorial, trabalha para o Woodgrove Bank como administrador de TI. Foi-lhe pedido que criasse um pacote de recursos para uma campanha de marketing que os utilizadores internos possam solicitar autosserviço. Os pedidos não requerem aprovação e o acesso do utilizador expira após 30 dias. Para este tutorial, os recursos da campanha de marketing são apenas membros de um único grupo, mas pode ser uma coleção de grupos, aplicações ou sites SharePoint Online.

![Descrição geral do cenário](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como recurso
> * Permitir que um utilizador no seu diretório solicite acesso
> * Demonstre como um utilizador interno pode solicitar o pacote de acesso

Para uma demonstração passo a passo do processo de implementação da gestão de direitos do Azure Ative Directory, incluindo a criação do seu primeiro pacote de acesso, veja o seguinte vídeo:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de direitos da Azure AD, deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença de Mobilidade Empresarial + Segurança (EMS) E5

Para mais informações, consulte [os requisitos da Licença.](entitlement-management-overview.md#license-requirements)

## <a name="step-1-set-up-users-and-group"></a>Passo 1: Configurar utilizadores e grupo

Um diretório de recursos tem um ou mais recursos para partilhar. Neste passo, cria-se um grupo chamado **Marketing resources** no diretório do Woodgrove Bank que é o recurso-alvo para a gestão de direitos. Também criou um solicitador interno.

**Papel pré-requisito:** Administrador global ou administrador de utilizador

![Criar utilizadores e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global ou administrador de utilizador.  

1. Na navegação à esquerda, clique no **Diretório Ativo Azure**.

1. Crie ou configure os seguintes dois utilizadores. Pode usar estes nomes ou nomes diferentes. **O Admin1** pode ser o utilizador em que está atualmente inscrito.

    | Nome | Função de diretório |
    | --- | --- |
    | **Administrador1** | Administrador global<br/>-ou-<br/>Administrador de utilizadores |
    | **Requestor1** | Utilizador |

1. Crie um grupo de segurança Azure AD chamado **Recursos de Marketing** com um tipo de membro atribuído . **Assigned**

    Este grupo será o recurso-alvo para a gestão de direitos. O grupo deve estar vazio de membros para começar.

## <a name="step-2-create-an-access-package"></a>Passo 2: Criar um pacote de acesso

Um pacote de *acesso* é um conjunto de recursos que uma equipa ou projeto precisa e é regido com políticas. Os pacotes de acesso são definidos em contentores *chamados catálogos.* Neste passo, cria-se um pacote de acesso à Campanha de **Marketing** no catálogo **Geral.**

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal Azure, na navegação à esquerda, clique no **Diretório Ativo Azure.**

1. No menu esquerdo, clique em **Governança de Identidade**

1. No menu esquerdo, clique em **pacotes de acesso**.  Se vir o **Access negado,** certifique-se de que está presente no seu diretório uma licença Azure AD Premium P2.

1. Clique em **Novo pacote de acesso**.

    ![Gestão de direitos no portal Azure](./media/entitlement-management-shared/access-packages-list.png)

1. No separador **Basics,** digite o pacote de acesso à Campanha de **Marketing** e descrição **O acesso aos recursos para a campanha**.

1. Deixe a lista de abandono do **Catálogo** definida para **o General**.

    ![Novo pacote de acesso - Separador Basics](./media/entitlement-management-access-package-first/basics.png)

1. Clique em **Seguir** para abrir o separador de **funções de Recurso.**

    Neste separador, seleciona os recursos e a função de recurso a incluir no pacote de acesso.

1. Clique **em Grupos e Equipas**.

1. No painel de grupos Select, encontre e selecione o grupo de **recursos de Marketing** que criou anteriormente.

    Por padrão, vê-se grupos dentro e fora do catálogo **geral.** Quando selecionar um grupo fora do catálogo **Geral,** será adicionado ao catálogo **Geral.**

    ![Novo pacote de acesso - Separador de funções de recursos](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Clique em **Selecionar** para adicionar o grupo à lista.

1. Na lista de abandono do **papel,** selecione **Member**.

    ![Novo pacote de acesso - Separador de funções de recursos](./media/entitlement-management-access-package-first/resource-roles.png)

1. Clique em **Seguir** para abrir o separador **Pedidos.**

    Neste separador, cria-se uma política de pedidos. Uma *política* define as regras ou guarda-costas para aceder a um pacote de acesso. Cria uma política que permite a um utilizador específico no diretório de recursos solicitar este pacote de acesso.

1. Nos **Utilizadores que podem solicitar** a secção de acesso, clique **para utilizadores no seu diretório** e, em seguida, clique em **utilizadores e grupos específicos**.

    ![Novo pacote de acesso - Separador de pedidos](./media/entitlement-management-access-package-first/requests.png)

1. Clique em **Adicionar utilizadores e grupos**.

1. No painel De utilizadores e grupos Select, selecione o utilizador **Requestor1** que criou anteriormente.

    ![Novo pacote de acesso - Separador de pedidos - Selecione utilizadores e grupos](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Clique em **Selecionar**.

1. Desloque-se para as secções **de Aprovação** e Ativação de **Pedidos.**

1. Licença **Exigir aprovação** definida para **Nº**.

1. Para **ativar pedidos,** clique **em Sim** para permitir que este pacote de acesso seja solicitado assim que for criado.

    ![Novo pacote de acesso - Separador de pedidos - Aprovação e Solicitações de Habilitação](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Clique em **seguida** para abrir o separador **Lifecycle.**

1. Na secção **Expiração,** as atribuições do pacote de **acesso expiram** até **ao número de dias**.

1. Definir **As atribuições expiram após** **30** dias.

    ![Novo pacote de acesso - Separador lifecycle](./media/entitlement-management-access-package-first/lifecycle.png)

1. Clique em **Seguir** para abrir o **separador Rever + Criar.**

    ![Novo pacote de acesso - Review + Criar separador](./media/entitlement-management-access-package-first/review-create.png)

    Após alguns momentos, deve ver uma notificação de que o pacote de acesso foi criado com sucesso.

1. No menu esquerdo do pacote de acesso da Campanha de Marketing, clique em **Visão Geral**.

1. Copie o **link do portal My Access**.

    Vais usar este link para o próximo passo.

    ![Visão geral do pacote de acesso - My Access portal link](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Passo 3: Solicitar acesso

Neste passo, executa os passos como **solicitador interno** e solicita o acesso ao pacote de acesso. Os solicitores submetem os seus pedidos usando um site chamado portal My Access. O portal My Access permite aos solicitantes submeter pedidos de pacotes de acesso, ver os pacotes de acesso a que já têm acesso e ver o seu histórico de pedidos.

**Papel pré-requisito:** Solicitador interno

1. Assine pelo portal Azure.

1. Numa nova janela de navegador, navegue para o portal My Access link que copiou no passo anterior.

1. Inscreva-se no portal My Access como **Requestor1**.

    Você deve ver o pacote de acesso da Campanha de **Marketing.**

1. Se necessário, na coluna **Descrição,** clique na seta para ver detalhes sobre o pacote de acesso.

    ![Meu portal de acesso - Pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de verificação para selecionar o pacote.

1. Clique em **Solicitar acesso** para abrir o painel de acesso do Pedido.

    ![Meu portal de acesso - Solicitar botão de acesso](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Na caixa de justificação do **Negócio,** escreva a justificação que estou a trabalhar na nova campanha de **marketing.**

    ![Meu portal de acesso - Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique em **Submeter**.

1. No menu esquerdo, clique no **histórico de solicitar** para verificar se o seu pedido foi submetido.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Passo 4: Validar que o acesso foi atribuído

Neste passo, confirma que o **solicitador interno** foi atribuído ao pacote de acesso e que agora são membros do grupo de **recursos de Marketing.**

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. Assine pelo portal My Access.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como **Admin1**.

1. Clique em **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso**.

1. Encontre e clique no pacote de acesso da Campanha de **Marketing.**

1. No menu esquerdo, clique em **Pedidos.**

    Deve consultar o Requestor1 e a política inicial com um estatuto de **Entregue**.

1. Clique no pedido para ver os detalhes do pedido.

    ![Pacote de acesso - Solicitar detalhes](./media/entitlement-management-access-package-first/request-details.png)

1. Na navegação à esquerda, clique no **Diretório Ativo Azure**.

1. Clique **em Grupos** e abra o grupo **de recursos de Marketing.**

1. Clique em **Membros**.

    Devia ver o **Requestor1** listado como membro.

    ![Membros de recursos de marketing](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Passo 5: Limpar os recursos

Neste passo, remove as alterações que efez e elimina o pacote de acesso à Campanha de **Marketing.**

**Papel pré-requisito:**  Administrador global ou administrador de utilizador

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. Abra o pacote de acesso à Campanha de **Marketing.**

1. Clique em **Tarefas**.

1. Para **Requestor1,** clique na elipse (**...**) e, em seguida, clique em Remover **o acesso**. Na mensagem que aparece, clique **em Sim**.

    Após alguns momentos, o estado passará de Entregue para Expirado.

1. Clique nas **funções de Recurso**.

1. Para **os recursos de Marketing,** clique na elipse **(...**) e clique em Remover a **função de recurso**. Na mensagem que aparece, clique **em Sim**.

1. Abra a lista de pacotes de acesso.

1. Para **campanha de marketing,** clique na elipse**e,** em seguida, clique em **Apagar**. Na mensagem que aparece, clique **em Sim**.

1. No Diretório Ativo Azure, elimine todos os utilizadores que tenha criado, tais como **Requestor1** e **Admin1**.

1. Eliminar o grupo **de recursos de Marketing.**

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para conhecer os passos de cenário comum na gestão de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
