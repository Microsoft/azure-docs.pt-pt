---
title: 'Tutorial: Configure BlueJeans para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador à BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059042"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configure BlueJeans para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no BlueJeans e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para blueJeans.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um locatário do Azure AD
* Um inquilino BlueJeans com o plano [My Company](https://www.BlueJeans.com/pricing) ou melhor habilitado
* Uma conta de utilizador em BlueJeans com permissões de administrador

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na [API BlueJeans,](https://BlueJeans.github.io/developer)que está disponível para equipas BlueJeans no plano Standard ou melhor.

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando BlueJeans da galeria

Antes de configurar o BlueJeans para o fornecimento automático de utilizadores com o Azure AD, precisa de adicionar BlueJeans da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar BlueJeans da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **BlueJeans,** selecione **BlueJeans** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

    ![BlueJeans na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Atribuir utilizadores a BlueJeans

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao BlueJeans. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao BlueJeans seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Dicas importantes para atribuir utilizadores ao BlueJeans

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao BlueJeans para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao BlueJeans, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configurar o fornecimento automático de utilizadores ao BlueJeans

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em BlueJeans com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para BlueJeans, seguindo as instruções fornecidas no [tutorial de inscrição individual BlueJeans](bluejeans-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para BlueJeans em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **BlueJeans**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BlueJeans**.

    ![O link BlueJeans na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Na secção **credenciais de administrador,** insera o nome de **utilizador do Administrador**e a **Palavra-passe do Administrador** da sua conta BlueJeans. Exemplos desses valores são:

   * No campo **De utilizador da Admin,** preencha o nome de utilizador da conta de administração no seu inquilino BlueJeans. Exemplo: admin@contoso.com.

   * No campo **'Palavra-passe' Do Administrador,** preencha a palavra-passe correspondente ao nome de utilizador do administrador.

6. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao BlueJeans. Se a ligação falhar, certifique-se de que a sua conta BlueJeans tem permissões de administrador e tente novamente.

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to BlueJeans**.

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para BlueJeans na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em BlueJeans para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para BlueJeans, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Defina os utilizadores e/ou grupos que deseja fornecer ao BlueJeans, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Fornecimento bluejeans](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no BlueJeans.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* Os Bluejeans não permitem nomes de utilizadores que excedam 30 caracteres.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
