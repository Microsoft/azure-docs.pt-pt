---
title: 'Tutorial: Configure Zscaler Beta para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Do Azure para fornecer e desfornecer automaticamente contas de utilizador para a Zscaler Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062746"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Beta para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Zscaler Beta e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para a Zscaler Beta.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../active-directory-saas-app-provisioning.md)
>


## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um inquilino da AD Azure
* Um inquilino Zscaler Beta
* Uma conta de utilizador em Zscaler Beta com permissões de Administrador

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na Zscaler Beta SCIM API, que está disponível para os desenvolvedores Beta Zscaler para contas com o pacote Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionando Zscaler Beta da galeria

Antes de configurar a Zscaler Beta para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar a Zscaler Beta da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Zscaler Beta na galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite O Zscaler Beta,** selecione **Zscaler Beta** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Atribuir utilizadores à Zscaler Beta

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler Beta. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Zscaler Beta seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Dicas importantes para atribuir utilizadores à Zscaler Beta

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Zscaler Beta para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Zscaler Beta, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurar o fornecimento automático de utilizadores à Zscaler Beta

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Zscaler Beta com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para a Zscaler Beta, seguindo as instruções fornecidas no tutorial de [inscrição única Zscaler Beta](zscaler-beta-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Zscaler Beta em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Zscaler Beta**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler Beta**.

    ![O link Beta Zscaler na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Na secção **credenciais de administrador,** insera o URL do **Inquilino** e o **Token Secreto** da sua conta Zscaler Beta, conforme descrito no Passo 6.

6. Para obter o URL do **Inquilino** e **token secreto,** navegue para **a Administração > Definições** de autenticação na interface de utilizador do portal Zscaler Beta e clique em **SAML** sob **tipo de autenticação**.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Clique em **Configure SAML** para abrir as opções **SAML de Configuração.**

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    **Selecione Ativar o fornecimento baseado no SCIM** para recuperar o **URL base** e o **token**do portador e, em seguida, guarde as definições. Copie o **URL base** para o URL do **Inquilino**e **Bearer Token** para **O Token Secreto** no portal Azure.

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Zscaler Beta. Se a ligação falhar, certifique-se de que a sua conta Zscaler Beta tem permissões de Administrador e tente novamente.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. No campo **de e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verifique a caixa de verificação Enviar uma notificação por **e-mail quando ocorrer uma falha**.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zscaler Beta**.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zscaler Beta na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Zscaler Beta para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Zscaler Beta**.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Zscaler Beta na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Zscaler Beta para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](./../active-directory-saas-scoping-filters.md).

15. Para ativar o serviço de provisionamento De AD Azure para a Zscaler Beta, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores e/ou grupos que deseja fornecer ao Zscaler Beta, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Fornecimento Beta Zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure em Zscaler Beta.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../active-directory-saas-provisioning-reporting.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
