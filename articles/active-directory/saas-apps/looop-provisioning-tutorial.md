---
title: 'Tutorial: Configure Looop para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão e desaconsebilização automática de contas de utilizador à Looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057448"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Tutorial: Configure Looop para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Looop e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desa provisionamento de utilizadores e/ou grupos à Looop.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de Looop](https://www.looop.co/pricing/)
* Uma conta de utilizador num Looop com permissões de administrador.

## <a name="assign-users-to-looop"></a>Atribuir utilizadores à Looop

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Looop. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Looop seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Dicas importantes para atribuir utilizadores à Looop

* Recomenda-se que um único utilizador AZure AD seja designado para a Looop para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Looop, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-looop-for-provisioning"></a>Criar Looop para provisionamento

Antes de configurar o Looop para o fornecimento automático do utilizador com a Azure AD, terá de obter algumas informações de fornecimento da Looop.

1. Inscreva-se na consola [de administração Looop](https://app.looop.co/#/login) e selecione **Conta.** Em **Definições de Conta** selecione **autenticação**.

    ![Looop Add SCIM](media/looop-provisioning-tutorial/admin.png)

2. Gere um novo token clicando **em Reset Token** em **integração SCIM**.

    ![Looop Add SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Copie o **ponto final scim** e **o token**. Estes valores serão introduzidos nos campos **URL** e **Secret Token** no separador Provisioning da sua aplicação Looop no portal Azure. 

    ![Looop Criar Token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Adicione Looop da galeria

Para configurar o Looop para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar looop da galeria de aplicações AD AD Azure à sua lista de aplicações geridas pelo SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Looop,** selecione **Looop** no painel de resultados. 

    ![Looop na lista de resultados](common/search-new-app.png)

5. Selecione o **botão Desinsubamento para Looop** que irá redirecioná-lo para a página de login do Looop. 

    ![Looop OIDC Adicionar](media/looop-provisioning-tutorial/signup.png)

6. Como o Looop é uma aplicação OpenIDConnect, opte por iniciar sessão no Looop utilizando a sua conta de trabalho da Microsoft.

    ![Login Looop OIDC](media/looop-provisioning-tutorial/msftlogin.png)

7. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será então automaticamente adicionada ao seu inquilino e você será redirecionado para a sua conta Looop.

    ![Consentimento looop OIDc](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configure o fornecimento automático de utilizadores ao Looop 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Looop com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para looop em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Looop**.

    ![O link Looop na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://<organisation_domain>.looop.co/scim/v2` na URL do **inquilino.** Por exemplo, `https://demo.looop.co/scim/v2`. Insira o valor que recuperou e guardou mais cedo de Looop em **Secret Token.** Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Looop. Se a ligação falhar, certifique-se de que a sua conta Looop tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Looop**.

    ![Mapeamentos de utilizadores de Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Looop na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Looop para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador looop](media/looop-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Meta Networks Connector**.

    ![Mapeamentos do Grupo Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Meta Networks Connector na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no Conector Meta Networks para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do Grupo Looop](media/looop-provisioning-tutorial/groupattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para looop, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à Looop, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AD Azure em Looop.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


