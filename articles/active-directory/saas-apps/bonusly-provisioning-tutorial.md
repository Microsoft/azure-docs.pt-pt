---
title: 'Tutorial: Configure Bonusly para o fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador à Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058882"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Tutorial: Configure bónus para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Bonusly e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos à Bonusly.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um locatário do Azure AD
* Um [inquilino bónus](https://bonus.ly/pricing)
* Uma conta de utilizador em Bonusly com permissões de Administrador

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na [API bonusly Rest,](https://konghq.com/solutions/gateway/)que está disponível para desenvolvedores bónus.

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando Bónus da galeria

Antes de configurar bónus para o fornecimento automático de utilizadores com a AD Azure, precisa adicionar Bónus da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Bónus à galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Bonusly**, selecione **Bonusly** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Bónus na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Atribuir utilizadores a Bónus

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido "atribuídos" a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de ter acesso à Bonusly. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Bonusly seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Dicas importantes para atribuir utilizadores à Bonusly

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Bonusly para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador à Bonusly, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configurar o fornecimento automático de utilizadores à Bonusly

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Bónus com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para bónus, seguindo as instruções fornecidas no tutorial de [inscrição bónus.](bonus-tutorial.md) O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para bónus em Anúncio sinuoso:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Bonusly**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Bonusly**.

    ![O link Bónus na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob a secção **de Credenciais de Administrador,** insera o **Token Secreto** da sua conta Bonusly conforme descrito no Passo 6.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. O **Token Secreto** para a sua conta Bonusly está localizado na **Admin > Company > Integrações.** Na secção **Se quiser codificar,** clique em **API > Crie um novo Token** de Acesso API para criar um novo Token Secreto.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. No ecrã seguinte, digite um nome para o token de acesso na caixa de texto fornecida e, em seguida, prima **Create Api Key**. O novo sinal de acesso aparecerá por alguns segundos num pop-up.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/Token02.png)

8. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se a Bonusly. Se a ligação falhar, certifique-se de que a sua conta Bonusly tem permissões de Administrador e tente novamente.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. No campo **de e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verifique a caixa de verificação Enviar uma notificação por **e-mail quando ocorrer uma falha**.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Clique em **Guardar**.

11. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Bonusly**.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Reveja os atributos do utilizador que são sincronizados de Azure AD para Bonusly na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Bonusly para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de provisionamento de AD Azure para bónus, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os utilizadores e/ou grupos que deseja fornecer à Bonusly, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Provisionamento Bónus](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure em Bonusly.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
