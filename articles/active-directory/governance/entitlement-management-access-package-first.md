---
title: Tutorial-criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Tutorial passo a passo sobre como criar seu primeiro pacote de acesso em Azure Active Directory gerenciamento de direitos.
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
ms.openlocfilehash: de4d4a1825149a512d7abdb192d8fb9d49e85a20
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174869"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Tutorial: criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure AD

O gerenciamento de acesso a todos os recursos de que os funcionários precisam, como grupos, aplicativos e sites, é uma função importante para as organizações. Você deseja conceder aos funcionários o nível certo de acesso de que eles precisam para serem produtivos e remover o acesso quando não for mais necessário.

Neste tutorial, você trabalha para o Banco Woodgrove como um administrador de ti. Você foi solicitado a criar um pacote de recursos para uma campanha de marketing que os usuários internos podem solicitar por autoatendimento. As solicitações não exigem aprovação e o acesso do usuário expira após 30 dias. Para este tutorial, os recursos da campanha de marketing são apenas membros de um único grupo, mas podem ser uma coleção de grupos, aplicativos ou sites do SharePoint Online.

![Descrição geral do cenário](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como um recurso
> * Permitir que um usuário em seu diretório solicite acesso
> * Demonstrar como um usuário interno pode solicitar o pacote de acesso

Para obter uma demonstração passo a passo do processo de implantação de Azure Active Directory gerenciamento de direitos, incluindo a criação de seu primeiro pacote de acesso, veja o vídeo a seguir:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Pré-requisitos

Para usar o gerenciamento de direitos do Azure AD, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença do Enterprise Mobility + Security (EMS) e5

Para obter mais informações, consulte [requisitos de licença](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Etapa 1: Configurar usuários e grupo

Um diretório de recursos tem um ou mais recursos para compartilhar. Nesta etapa, você cria um grupo chamado **recursos de marketing** no diretório do Banco Woodgrove, que é o recurso de destino para gerenciamento de direitos. Você também configura um solicitante interno.

**Função de pré-requisito:** Administrador global ou administrador de usuários

![Criar usuários e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou administrador de usuários.  

1. No painel de navegação esquerdo, clique em **Azure Active Directory**.

1. Crie ou configure os dois usuários a seguir. Você pode usar esses nomes ou nomes diferentes. **Admin1** pode ser o usuário no qual você está conectado no momento.

    | Nome | Função de diretório |
    | --- | --- |
    | **Admin1** | Administrador global<br/>-ou-<br/>Administrador do usuário |
    | **Requestor1** | Utilizador |

1. Crie um grupo de segurança do Azure AD chamado **recursos de marketing** com um tipo de associação **atribuído**.

    Esse grupo será o recurso de destino para gerenciamento de direitos. O grupo deve estar vazio de membros para iniciar.

## <a name="step-2-create-an-access-package"></a>Etapa 2: criar um pacote de acesso

Um *pacote do Access* é um pacote de recursos que uma equipe ou projeto precisa e é regido por políticas. Os pacotes de acesso são definidos em contêineres chamados *catálogos*. Nesta etapa, você cria um pacote de acesso de **campanha de marketing** no catálogo **geral** .

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal do Azure, no painel de navegação esquerdo, clique em **Azure Active Directory**.

1. No menu à esquerda, clique em **governança de identidade**

1. No menu à esquerda, clique em **pacotes de acesso**.  Se você vir **acesso negado**, verifique se uma licença Azure ad Premium P2 está presente no seu diretório.

1. Clique em **novo pacote de acesso**.

    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-shared/access-packages-list.png)

1. Na guia **noções básicas** , digite o nome do pacote de acesso **da campanha de marketing** e o **acesso de descrição aos recursos da campanha**.

1. Deixe a lista suspensa **Catálogo** definida como **geral**.

    ![Novo pacote de acesso-guia noções básicas](./media/entitlement-management-access-package-first/basics.png)

1. Clique em **Avançar** para abrir a guia **funções de recurso** .

    Nessa guia, você seleciona os recursos e a função de recurso para incluir no pacote de acesso.

1. Clique em **grupos e equipes**.

1. No painel Selecionar grupos, localize e selecione o grupo de **recursos de marketing** que você criou anteriormente.

    Por padrão, você vê grupos dentro e fora do catálogo **geral** . Quando você seleciona um grupo fora do catálogo **geral** , ele será adicionado ao catálogo **geral** .

    ![Novo pacote de acesso – guia funções de recurso](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Clique em **selecionar** para adicionar o grupo à lista.

1. Na lista suspensa **função** , selecione **membro**.

    ![Novo pacote de acesso – guia funções de recurso](./media/entitlement-management-access-package-first/resource-roles.png)

1. Clique em **Avançar** para abrir a guia **solicitações** .

    Nessa guia, você cria uma política de solicitação. Uma *política* define as regras ou guardrails para acessar um pacote de acesso. Você cria uma política que permite que um usuário específico no diretório de recursos solicite esse pacote de acesso.

1. Na seção **usuários que podem solicitar acesso** , clique em **para usuários em seu diretório** e clique em **usuários e grupos específicos**.

    ![Novo pacote de acesso-guia solicitações](./media/entitlement-management-access-package-first/requests.png)

1. Clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione o usuário **Requestor1** que você criou anteriormente.

    ![Novo pacote de acesso-guia solicitações-Selecionar usuários e grupos](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Clique em **Selecionar**.

1. Role para baixo até as seções **aprovação** e **habilitar solicitações** .

1. Deixe **exigir aprovação** definida como **não**.

1. Para **habilitar solicitações**, clique em **Sim** para habilitar esse pacote de acesso para ser solicitado assim que ele for criado.

    ![Novo pacote de acesso-guia solicitações-aprovação e habilitar solicitações](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Clique em **Avançar** para abrir a guia **ciclo de vida** .

1. Na seção **expiração** , defina **acesso atribuições de pacote expirar** para **número de dias**.

1. A definição das **atribuições expira após** **30** dias.

    ![Novo pacote de acesso-guia ciclo de vida](./media/entitlement-management-access-package-first/lifecycle.png)

1. Clique em **Avançar** para abrir a guia **revisar + criar** .

    ![Novo pacote de acesso-guia examinar + criar](./media/entitlement-management-access-package-first/review-create.png)

    Após alguns instantes, você deverá ver uma notificação de que o pacote de acesso foi criado com êxito.

1. No menu à esquerda do pacote de acesso da campanha de marketing, clique em **visão geral**.

1. Copie o **link meu portal de acesso**.

    Você usará esse link para a próxima etapa.

    ![Visão geral do pacote de acesso – link meu portal de acesso](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Etapa 3: solicitar acesso

Nesta etapa, você executa as etapas como **solicitante interno** e solicita acesso ao pacote de acesso. Os solicitantes enviam suas solicitações usando um site chamado meu portal de acesso. O portal meu acesso permite que os solicitantes enviem solicitações para pacotes do Access, consulte os pacotes de acesso aos quais eles já têm acesso e exibam o histórico de solicitações.

**Função de pré-requisito:** Solicitante interno

1. Saia do portal do Azure.

1. Em uma nova janela do navegador, navegue até o link meu portal de acesso que você copiou na etapa anterior.

1. Entre no portal meu acesso como **Requestor1**.

    Você deve ver o pacote de acesso à **campanha de marketing** .

1. Se necessário, na coluna **Descrição** , clique na seta para exibir detalhes sobre o pacote de acesso.

    ![Meu Portal de acesso-pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de seleção para selecionar o pacote.

1. Clique em **solicitar acesso** para abrir o painel solicitar acesso.

    ![Meu Portal de acesso – botão solicitar acesso](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Na caixa **justificativa de negócios** , digite a justificativa **que estou trabalhando na nova campanha de marketing**.

    ![Meu Portal de acesso-solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique em **Enviar**.

1. No menu à esquerda, clique em **histórico de solicitações** para verificar se sua solicitação foi enviada.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Etapa 4: validar que o acesso foi atribuído

Nesta etapa, você confirma que o **solicitante interno** recebeu o pacote de acesso e que agora ele é um membro do grupo de **recursos de marketing** .

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Saia do meu portal de acesso.

1. Entre no [portal do Azure](https://portal.azure.com) como **admin1**.

1. Clique em **Azure Active Directory** e em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso**.

1. Localize e clique em pacote de acesso à **campanha de marketing** .

1. No menu à esquerda, clique em **solicitações**.

    Você deve ver Requestor1 e a política inicial com o status de **entregue**.

1. Clique na solicitação para ver os detalhes da solicitação.

    ![Pacotes de acesso-detalhes da solicitação](./media/entitlement-management-access-package-first/request-details.png)

1. No painel de navegação esquerdo, clique em **Azure Active Directory**.

1. Clique em **grupos** e abra o grupo **recursos de marketing** .

1. Clique em **Membros**.

    Você deve ver **Requestor1** listadas como um membro.

    ![Membros de recursos de marketing](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Etapa 5: limpar os recursos

Nesta etapa, você remove as alterações feitas e exclui o pacote de acesso à **campanha de marketing** .

**Função de pré-requisito:**  Administrador global ou administrador de usuários

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. Abra o pacote de acesso à **campanha de marketing** .

1. Clique em **atribuições**.

1. Para **Requestor1**, clique nas reticências ( **...** ) e, em seguida, clique em **Remover acesso**. Na mensagem que aparece, clique em **Sim**.

    Após alguns instantes, o status será alterado de entregue para expirado.

1. Clique em **funções de recurso**.

1. Para **recursos de marketing**, clique nas reticências ( **...** ) e, em seguida, clique em **remover função de recurso**. Na mensagem que aparece, clique em **Sim**.

1. Abra a lista de pacotes de acesso.

1. Para **campanha de marketing**, clique nas reticências ( **...** ) e, em seguida, clique em **excluir**. Na mensagem que aparece, clique em **Sim**.

1. Em Azure Active Directory, exclua todos os usuários que você criou, como **Requestor1** e **admin1**.

1. Exclua o grupo de **recursos de marketing** .

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para saber mais sobre as etapas comuns do cenário no gerenciamento de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
