---
title: Tutorial-criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Tutorial passo a passo sobre como criar seu primeiro pacote de acesso em Azure Active Directory gerenciamento de direitos (versão prévia).
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489171"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Tutorial: Criar seu primeiro pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O gerenciamento de acesso a todos os recursos de que os funcionários precisam, como grupos, aplicativos e sites, é uma função importante para as organizações. Você deseja conceder aos funcionários o nível certo de acesso de que eles precisam para serem produtivos e remover o acesso quando não for mais necessário.

Neste tutorial, você trabalha para o Banco Woodgrove como um administrador de ti. Você foi solicitado a criar um pacote de recursos para um projeto Web que os usuários internos possam solicitar por autoatendimento. As solicitações exigem aprovação e o acesso do usuário expira após 30 dias. Para este tutorial, os recursos do projeto Web são apenas uma associação em um único grupo, mas pode ser uma coleção de grupos, aplicativos ou sites do SharePoint Online.

![Descrição geral do cenário](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como um recurso
> * Designar um Aprovador
> * Demonstrar como um usuário interno pode solicitar o pacote de acesso
> * Aprovar a solicitação de acesso

Se você não tiver uma licença Azure AD Premium P2 ou Enterprise Mobility + Security e5, crie uma [avaliação gratuita Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Pré-requisitos

Para usar o gerenciamento de direitos do Azure AD (versão prévia), você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença do Enterprise Mobility + Security (EMS) e5

## <a name="step-1-set-up-users-and-group"></a>Passo 1: Configurar usuários e grupo

Um diretório de recursos tem um ou mais recursos para compartilhar. Nesta etapa, você cria um grupo chamado **grupo de engenharia** no diretório do Banco Woodgrove que é o recurso de destino para gerenciamento de direitos. Você também configura um solicitante interno.

**Função de pré-requisito:** Administrador global ou administrador de usuários

![Criar usuários e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou administrador de usuários.  

1. No painel de navegação esquerdo, clique em **Azure Active Directory**.

1. Crie ou configure os dois usuários a seguir. Você pode usar esses nomes ou nomes diferentes. **Admin1** pode ser o usuário no qual você está conectado no momento.

    | Nome | Função de diretório | Descrição |
    | --- | --- | --- |
    | **Admin1** | Administrador Global<br/>-ou-<br/>Administrador limitado (administrador do usuário) | Administrador e aprovador |
    | **Requestor1** | Utilizador | Solicitante interno |

    Para este tutorial, o administrador e o aprovador são a mesma pessoa, mas você normalmente designa uma ou mais pessoas para serem aprovadas.

1. Crie um grupo de segurança do Azure AD chamado **grupo de engenharia** com um tipo de associação **atribuído**.

    Esse grupo será o recurso de destino para gerenciamento de direitos. O grupo deve estar vazio de membros para iniciar.

## <a name="step-2-create-an-access-package"></a>Passo 2: Criar um pacote de acesso

Um *pacote do Access* é um grupo de todos os recursos que um usuário precisa para trabalhar em um projeto ou executar seu trabalho. Os pacotes de acesso são definidos em contêineres chamados catálogos. Nesta etapa, você cria um **pacote de acesso do projeto Web** no catálogo **geral** .

**Função de pré-requisito:** Administrador global ou administrador de usuários

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal do Azure, no painel de navegação esquerdo, clique em **Azure Active Directory**.

1. No menu à esquerda, clique em **governança de identidade**

1. No menu à esquerda, clique em **pacotes de acesso**.  Se você vir **acesso negado**, verifique se há uma licença Azure ad Premium P2 presente nesse diretório.

1. Clique em **novo pacote de acesso**.

    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Na guia **noções básicas** , digite o nome pacote de acesso do **projeto Web** e descrição **do pacote de acesso para o projeto Web de engenharia**.

1. Deixe a lista suspensa **Catálogo** definida como **geral**.

    ![Novo pacote de acesso-guia noções básicas](./media/entitlement-management-access-package-first/basics.png)

1. Clique em **Avançar** para abrir a guia **funções de recurso** .

    Nessa guia, você seleciona as permissões a serem incluídas no pacote de acesso.

1. Clique em **grupos**.

1. No painel Selecionar grupos, localize e selecione o grupo de **grupos de engenharia** que você criou anteriormente.

    Por padrão, você vê grupos dentro e fora do catálogo **geral** . Quando você seleciona um grupo fora do catálogo **geral** , ele será adicionado ao catálogo **geral** .

    ![Novo pacote de acesso – guia funções de recurso](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Clique em **selecionar** para adicionar o grupo à lista.

1. Na lista suspensa **função** , selecione **membro**.

    ![Novo pacote de acesso – guia funções de recurso](./media/entitlement-management-access-package-first/resource-roles.png)

1. Clique em **Avançar** para abrir a guia **política** .

1. Defina a alternância **criar primeira política** para **mais tarde**.

    Você criará a política na próxima seção.

    ![Novo pacote de acesso-guia política](./media/entitlement-management-access-package-first/policy.png)

1. Clique em **Avançar** para abrir a guia revisar **+ criar** .

    ![Novo pacote de acesso-guia examinar + criar](./media/entitlement-management-access-package-first/review-create.png)

1. Examine as configurações do pacote de acesso e clique em **criar**.

    Você poderá ver uma mensagem informando que o pacote de acesso não ficará visível para os usuários porque o catálogo ainda não está habilitado.

    ![Novo pacote de acesso-mensagem não visível](./media/entitlement-management-access-package-first/not-visible.png)

1. Clique em **OK**.

    Após alguns instantes, você deverá ver uma notificação de que o pacote de acesso foi criado com êxito.

## <a name="step-3-create-a-policy"></a>Passo 3: Criar uma política

Uma *política* define as regras ou guardrails para acessar um pacote de acesso. Nesta etapa, você cria uma política que permite que um usuário específico no diretório de recursos solicite o pacote de acesso. Você também pode especificar que as solicitações devem ser aprovadas e quem será o aprovador.

![Criar uma política de pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Função de pré-requisito:** Administrador global ou administrador de usuários

1. No **pacote de acesso do projeto Web**, no menu à esquerda, clique em **políticas**.

    ![Lista de políticas do pacote de acesso](./media/entitlement-management-access-package-first/policies-list.png)

1. Clique em **Adicionar política** para abrir criar política.

1. Digite o nome **política** e descrição do solicitante interno **permite que os usuários neste diretório solicitem acesso aos recursos do projeto Web**.

1. Na seção **usuários que podem solicitar acesso** , clique em **para usuários em seu diretório**.

    ![Criar política](./media/entitlement-management-access-package-first/policy-create.png)

1. Role para baixo até a seção **Selecionar usuários e grupos** e clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione o usuário **Requestor1** que você criou anteriormente e, em seguida, clique em **selecionar**.

1. Na seção **solicitação** , defina **exigir aprovação** como **Sim**.

1. Na seção **selecionar aprovadores** , clique em **Adicionar aprovadores**.

1. No painel Selecionar Aprovadores, selecione o **admin1** que você criou anteriormente e, em seguida, clique em **selecionar**.

    Para este tutorial, o administrador e o aprovador são a mesma pessoa, mas você pode designar outra pessoa como um Aprovador.

1. Na seção **expiração** , definir o **pacote de acesso expira** para o **número de dias**.

1. O **acesso definido expira após** **30** dias.

1. Para **habilitar política**, clique em **Sim**.

    ![Criar configurações de política](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Clique em **criar** para criar a **política interna**do solicitante.

1. No menu à esquerda do pacote de acesso do projeto Web, clique em **visão geral**.

1. Copie o **link meu portal de acesso**.

    Você usará esse link para a próxima etapa.

    ![Visão geral do pacote de acesso – link meu portal de acesso](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Passo 4: Pedir acesso

Nesta etapa, você executa as etapas como solicitante **interno** e solicita acesso ao pacote de acesso. Os solicitantes enviam suas solicitações usando um site chamado meu portal de acesso. O portal meu acesso permite que os solicitantes enviem solicitações para pacotes do Access, consulte os pacotes de acesso aos quais eles já têm acesso e exibam o histórico de solicitações.

**Função de pré-requisito:** Solicitante interno

1. Saia do portal do Azure.

1. Em uma nova janela do navegador, navegue até o link meu portal de acesso que você copiou na etapa anterior.

1. Entre no portal meu acesso como **Requestor1**.

    Você deve ver o **pacote de acesso do projeto Web**.

1. Se necessário, na coluna **Descrição** , clique na seta para exibir detalhes sobre o pacote de acesso.

    ![Meu Portal de acesso-pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de seleção para selecionar o pacote.

1. Clique em **solicitar acesso** para abrir o painel solicitar acesso.

1. Na caixa **justificativa de negócios** , digite a justificativa **trabalhando no projeto Web**.

1. Defina a **solicitação para período específico** alternar para **Sim**.

1. Defina a **data de início** como hoje e a **data de término** como amanhã.

    ![Meu Portal de acesso-solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique em **Submit** (Submeter).

1. No menu à esquerda, clique em **histórico de solicitações** para verificar se sua solicitação foi enviada.

## <a name="step-5-approve-access-request"></a>Passo 5: Aprovar solicitação de acesso

Nesta etapa, você entra como o usuário **Aprovador** e aprova a solicitação de acesso para o solicitante interno. Os aprovadores usam o mesmo meu portal de acesso que os solicitantes usam para enviar solicitações. Usando o meu portal de acesso, os aprovadores podem exibir as aprovações pendentes e aprovar ou negar solicitações.

**Função de pré-requisito:** Aprovador

1. Saia do meu portal de acesso.

1. Entre no [portal meu acesso](https://myaccess.microsoft.com) como **admin1**.

1. No menu à esquerda, clique em aprovações.

1. Na guia **pendente** , localize **Requestor1**.

    Se você não vir a solicitação de Requestor1, aguarde alguns minutos e tente novamente.

1. Clique no link **Exibir** para abrir o painel solicitação de acesso.

1. Clique em **aprovar**.

1. Na caixa **motivo** , digite o motivo do **acesso aprovado para o projeto Web**.

    ![Meu Portal de acesso-solicitação de acesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Clique em **Enviar** para enviar sua decisão.

    Você deverá ver uma mensagem de que foi aprovada com êxito.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Passo 6: Validar que o acesso foi atribuído

Agora que você aprovou a solicitação de acesso, nesta etapa, você confirma que o **solicitante interno** recebeu o pacote de acesso e que agora ele é um membro do grupo de **grupos de engenharia** .

**Função de pré-requisito:** Administrador global ou administrador de usuários

1. Saia do meu portal de acesso.

1. Entre no [portal do Azure](https://portal.azure.com) como o **admin1**.

1. Clique em **Azure Active Directory** e em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso**.

1. Localize e clique em **pacote de acesso do projeto Web**.

1. No menu à esquerda, clique em **solicitações**.

    Você deve ver Requestor1 e a política de solicitante interna com o status de **entregue**.

1. Clique na solicitação para ver os detalhes da solicitação.

    ![Pacotes de acesso-detalhes da solicitação](./media/entitlement-management-access-package-first/request-details.png)

1. No painel de navegação esquerdo, clique em **Azure Active Directory**.

1. Clique em **grupos** e abra o grupo **grupo de engenharia** .

1. Clique em **Membros**.

    Você deve ver **Requestor1** listadas como um membro.

    ![Membros do grupo de engenharia](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Passo 7: Limpar recursos

Nesta etapa, você remove as alterações feitas e exclui o pacote de acesso do **pacote de acesso ao projeto Web** .

**Função de pré-requisito:**  Administrador global ou administrador de usuários

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. Abra o **pacote de acesso ao projeto Web**.

1. Clique em atribuições.

1. Para **Requestor1**, clique nas reticências ( **...** ) e, em seguida, clique em **Remover acesso**.

    O status será alterado de entregue para expirado.

1. Clique em **políticas**.

1. Para a **política do solicitante interno**, clique nas reticências ( **...** ) e, em seguida, clique em **excluir**.

1. Clique em **funções de recurso**.

1. Para **grupo de engenharia**, clique nas reticências ( **...** ) e, em seguida, clique em **remover função de recurso**.

1. Abra a lista de pacotes de acesso.

1. Para **projeto de acesso ao projeto Web**, clique nas reticências ( **...** ) e, em seguida, clique em **excluir**.

1. Em Azure Active Directory, exclua todos os usuários que você criou, como **Requestor1** e **admin1**.

1. Exclua o grupo de **grupos de engenharia** .

## <a name="next-steps"></a>Passos Seguintes

Avance para o próximo artigo para saber mais sobre as etapas comuns do cenário no gerenciamento de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
