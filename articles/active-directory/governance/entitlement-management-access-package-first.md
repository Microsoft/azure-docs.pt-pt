---
title: Tutorial - criar seu primeiro pacote de acesso na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Tutorial passo a passo para saber como criar seu primeiro pacote de acesso na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873504"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Tutorial: Crie seu primeiro pacote de acesso na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gerir o acesso a todos os recursos funcionários necessidade, como grupos, aplicações e sites, é uma função importante para organizações. Pretende conceder o nível certo de que precisam para serem produtivos e remova o acesso quando já não é necessário de acesso de funcionários.

Neste tutorial, vai trabalhar para o Woodgrove Bank como um administrador de TI. Ser solicitado para criar um pacote de recursos para um projeto da web que os usuários internos podem self-service a pedido. Pedidos de exigem a aprovação e acesso de utilizador expira após 30 dias. Para este tutorial, os recursos de projeto da web são apenas a associação a um único grupo, mas pode ser uma coleção de grupos, aplicações ou sites do SharePoint Online.

![Descrição geral do cenário](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um pacote de acesso com um grupo como um recurso
> * Designar um aprovador
> * Demonstrar como um usuário interno pode solicitar o pacote de acesso
> * Aprovar o pedido de acesso

Se não tiver um Azure AD Premium P2 ou Enterprise Mobility + Security E5 licença, crie um livre [Enterprise Mobility + Security E5 avaliação](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a gestão de direitos do Azure AD (pré-visualização), tem de ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 licença

## <a name="step-1-set-up-users-and-group"></a>Passo 1: Configurar utilizadores e de grupo

Um diretório de recursos tem um ou mais recursos para partilhar. Neste passo, vai criar um grupo denominado **grupo de engenharia** no diretório do Woodgrove Bank que é o recurso de destino para a gestão de direitos. Também configurar um requerente interno.

**Função de pré-requisitos:** Administrador global ou administrador de utilizadores

![Criar utilizadores e grupos](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um Administrador Global ou administrador de utilizadores.  

1. No painel de navegação esquerdo, clique em **do Azure Active Directory**.

1. Crie ou configure os seguintes dois utilizadores. Pode usar esses nomes ou nomes diferentes. **Admin1** pode ser o utilizador tem atualmente sessão iniciada como.

    | Name | Função de diretório | Descrição |
    | --- | --- | --- |
    | **admin1** | Administrador global<br/>-ou-<br/>Administrador limitado (administrador de utilizadores) | Administrador e aprovador |
    | **Requestor1** | Utilizador | Requerente interno |

    Para este tutorial, o administrador e o aprovador são a mesma pessoa, mas normalmente designar uma ou mais pessoas para ser aprovadores.

1. Criar uma segurança do Azure AD com o nome do grupo **grupo de engenharia** com um tipo de associação **atribuído**.

    Este grupo será o recurso de destino para a gestão de direitos. O grupo deve estar em branco de membros para iniciar.

## <a name="step-2-create-an-access-package"></a>Passo 2: Criar um pacote de acesso

Uma *pacote de acesso* é um pacote de todos os recursos de um utilizador tem de trabalhar num projeto ou executar seu trabalho. Pacotes de acesso são definidas nos contentores chamados *catálogos*. Neste passo, vai criar um **pacote de acesso do projeto de Web** no **geral** catálogo.

**Função de pré-requisitos:** Administrador global ou administrador de utilizadores

![Criar um pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. No portal do Azure, na navegação à esquerda, clique em **do Azure Active Directory**.

1. No menu à esquerda, clique em **governação de identidade**

1. No menu à esquerda, clique em **aceder a pacotes**.  Se vir **acesso negado**, certifique-se de que existe uma licença do Azure AD Premium P2 nesse diretório.

1. Clique em **novo pacote de acesso**.

    ![Gestão de direitos no portal do Azure](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Sobre o **Noções básicas** separador, escreva o nome **pacote de acesso do projeto de Web** e a descrição **pacote de acesso para o projeto de web de engenharia**.

1. Deixe o **catálogo** na lista pendente definida como **geral**.

    ![Novo pacote de acesso - separador de noções básicas](./media/entitlement-management-access-package-first/basics.png)

1. Clique em **próxima** para abrir o **funções de recursos** separador.

    Neste separador, selecione as permissões para incluir no pacote de acesso.

1. Clique em **grupos**.

1. No painel de grupos selecionados, localize e selecione o **grupo de engenharia** grupo que criou anteriormente.

    Por predefinição, verá que os grupos de dentro e fora do **gerais** catálogo. Quando seleciona um grupo de fora do **gerais** catálogo, este será adicionado para o **geral** catálogo.

    ![Novo pacote de acesso - separador de funções de recursos](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Clique em **selecione** para adicionar o grupo à lista.

1. Na **função** na lista pendente, selecione **membro**.

    ![Novo pacote de acesso - separador de funções de recursos](./media/entitlement-management-access-package-first/resource-roles.png)

1. Clique em **próxima** para abrir o **política** separador.

1. Definir o **criar a primeira diretiva** alternar para **mais tarde**.

    Irá criar a política na próxima seção.

    ![Novo pacote de acesso - separador de política](./media/entitlement-management-access-package-first/policy.png)

1. Clique em **próxima** para abrir o **rever + criar** separador.

    ![Novo pacote de acesso - rever + criar separador](./media/entitlement-management-access-package-first/review-create.png)

1. Reveja as definições do pacote de acesso e, em seguida, clique em **criar**.

    Poderá ver uma mensagem que o pacote de acesso não serão visível para os utilizadores porque o catálogo ainda não está ativado.

    ![Novo pacote de acesso - mensagem invisível](./media/entitlement-management-access-package-first/not-visible.png)

1. Clique em **OK**.

    Após alguns instantes, deverá ver uma notificação que o pacote de acesso foi criado com êxito.

## <a name="step-3-create-a-policy"></a>Passo 3: Criar uma política

R *política* define as regras ou guardrails para aceder a um pacote de acesso. Neste passo, vai criar uma política que permite que um utilizador específico no diretório de recursos para pedir o pacote de acesso. Também especificar que os pedidos devem ser aprovados e quem será o aprovador.

![Criar uma política de pacote de acesso](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Função de pré-requisitos:** Administrador global ou administrador de utilizadores

1. Na **pacote de acesso do projeto de Web**, no menu à esquerda, clique em **políticas**.

    ![Lista de políticas de pacote de acesso](./media/entitlement-management-access-package-first/policies-list.png)

1. Clique em **Adicionar política** para abrir a política de criar.

1. Escreva o nome **política de requerente interna** e uma descrição **permite que os utilizadores neste diretório para solicitar acesso a recursos de projeto da web**.

1. Na **usuários que podem pedir acesso** secção, clique em **para os utilizadores no seu diretório**.

    ![Criar política](./media/entitlement-management-access-package-first/policy-create.png)

1. Desloque para baixo para o **selecionar utilizadores e grupos** secção e clique em **adicionar utilizadores e grupos**.

1. No painel de grupos e selecionar utilizadores, selecione o **Requestor1** utilizador que criou anteriormente e clique em **selecione**.

1. Na **pedir** secção, defina **exigir a aprovação** para **Sim**.

1. Na **selecionar aprovadores** secção, clique em **adicionar aprovadores**.

1. No painel de aprovadores selecione, selecione o **Admin1** que criou anteriormente e, em seguida, clique em **selecione**.

    Para este tutorial, o administrador e o aprovador são a mesma pessoa, mas pode designar outra pessoa como um aprovador.

1. Na **expiração** secção, defina **pacote de acesso expira** para **número de dias**.

1. Definir **acesso expira após** ao **30** dias.

1. Para **ativar política**, clique em **Sim**.

    ![Criar definições de política](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Clique em **Create** para criar o **política de requerente interno**.

1. No menu à esquerda do pacote de acesso do projeto de Web, clique em **descrição geral**.

1. Copiar o **hiperligação do portal do meu acesso**.

    Irá utilizar esta ligação para a próxima etapa.

    ![Descrição geral do pacote acesso - hiperligação do portal do meu acesso](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Passo 4: Pedir acesso

Neste passo, vai realizar os passos como o **requerente interno** e pedir acesso ao pacote de acesso. Requerentes submetem os pedidos através de um site chamado o portal de acesso de My. O portal de acesso My permite requerentes submeter pedidos para os pacotes de acesso, consulte os pacotes de acesso que já têm acesso a e ver o respetivo histórico de pedidos.

**Função de pré-requisitos:** Requerente interno

1. Terminar sessão do portal do Azure.

1. Numa nova janela do browser, navegue para a hiperligação do portal do meu acesso que copiou no passo anterior.

1. Inicie sessão no portal do meu acesso como **Requestor1**.

    Deverá ver o **pacote de acesso do projeto de Web**.

1. Se necessário, no **Descrição** coluna, clique na seta para ver detalhes sobre o pacote de acesso.

    ![Meu portal de acesso - pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique na marca de verificação para selecionar o pacote.

1. Clique em **pedir acesso** para abrir o painel de acesso do pedido.

1. Na **justificação de negócio** , escreva a justificativa **trabalhar num projeto web**.

1. Definir o **pedir para o período específico** alternar para **Sim**.

1. Definir o **data de início** de hoje e **data de fim** para amanhã.

    ![Meu portal de acesso – pedir acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Clique em **Submit** (Submeter).

1. No menu à esquerda, clique em **histórico de pedidos** para verificar que o seu pedido foi submetido.

## <a name="step-5-approve-access-request"></a>Passo 5: Aprovar pedido de acesso

Neste passo, iniciar sessão como o **aprovador** utilizador e aprovar o pedido de acesso para o requerente interno. Os aprovadores utilizam o portal de meu acesso mesmo como usar o requerentes para submeter pedidos. Com o portal de meu acesso, os aprovadores podem ver aprovações pendentes e aprovar ou negar pedidos.

**Função de pré-requisitos:** Aprovador

1. Inicie sessão fora do portal do meu acesso.

1. Entrar para o [portal de acesso de meu](https://myaccess.microsoft.com) como **Admin1**.

1. No menu à esquerda, clique em **aprovações**.

1. Sobre o **pendente** separador, encontre **Requestor1**.

    Se não vir o pedido do Requestor1, aguarde alguns minutos e tente novamente.

1. Clique nas **vista** link para abrir o painel de pedido de acesso.

1. Clique em **aprovar**.

1. Na **motivo** , escreva o motivo pelo qual **aprovado acesso para o projeto de web**.

    ![Meu portal de acesso - pedido de acesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Clique em **submeter** para submeter a sua decisão.

    Deverá ver uma mensagem que foi aprovado com êxito.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Passo 6: Validar a que foi atribuído acesso

Agora que aprovou o pedido de acesso, neste passo, confirme que o **requerente interna** foi atribuído o acesso a pacote e que agora é um membro da **grupo engenharia** grupo.

**Função de pré-requisitos:** Administrador global ou administrador de utilizadores

1. Inicie sessão fora do portal do meu acesso.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como o **Admin1**.

1. Clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes**.

1. Encontre e clique em **pacote de acesso do projeto de Web**.

1. No menu à esquerda, clique em **pedidos**.

    Deverá ver Requestor1 e a política de requerente interno com o estado **entregues**.

1. Clique no pedido para ver os detalhes de pedido.

    ![Pacote de acesso - detalhes do pedido](./media/entitlement-management-access-package-first/request-details.png)

1. No painel de navegação esquerdo, clique em **do Azure Active Directory**.

1. Clique em **grupos** e abra o **grupo engenharia** grupo.

1. Clique em **membros**.

    Verá **Requestor1** listado como um membro.

    ![Membros do grupo de engenharia](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Passo 7: Limpar recursos

Neste passo, remova as alterações que efetuou e eliminar a **pacote de acesso do projeto de Web** pacote de acesso.

**Função de pré-requisitos:**  Administrador global ou administrador de utilizadores

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. Open **pacote de acesso do projeto de Web**.

1. Clique em **atribuições**.

1. Para **Requestor1**, clique nas reticências (**...** ) e, em seguida, clique em **remover o acesso**.

    O estado será alterado de entregues para expirado.

1. Clique em **políticas**.

1. Para **política de requerente interna**, clique nas reticências (**...** ) e, em seguida, clique em **eliminar**.

1. Clique em **funções de recursos**.

1. Para **grupo de engenharia**, clique nas reticências (**...** ) e, em seguida, clique em **remover função de recursos**.

1. Abra a lista de pacotes de acesso.

1. Para **projeto de acesso do projeto de Web**, clique nas reticências (**...** ) e, em seguida, clique em **eliminar**.

1. No Azure Active Directory, elimine todos os utilizadores que criou como **Requestor1** e **Admin1**.

1. Eliminar a **grupo de engenharia** grupo.

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para saber mais sobre os passos do cenário comum na gestão de direitos.
> [!div class="nextstepaction"]
> [Cenários comuns](entitlement-management-scenarios.md)
