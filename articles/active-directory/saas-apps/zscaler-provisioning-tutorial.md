---
title: 'Tutorial: Configurar o Zscaler para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d88aae8887cbfd872a8d3e82fb2166043c312e5a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515401"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Zscaler.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem o seguinte:

* Um inquilino do Azure AD.
* Um locatário do Zscaler.
* Uma conta de usuário no Zscaler com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API Zscaler SCIM, que está disponível para desenvolvedores Zscaler para contas com o pacote Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Adicionando o Zscaler da Galeria

Antes de configurar o Zscaler para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Zscaler da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler**, selecione **Zscaler** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Atribuindo usuários ao Zscaler

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Zscaler. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Zscaler seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Dicas importantes para atribuir usuários ao Zscaler

* É recomendável que um único usuário do Azure AD seja atribuído ao Zscaler para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Zscaler, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configurando o provisionamento automático de usuário para o Zscaler

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Zscaler com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Zscaler, seguindo as instruções fornecidas no [tutorial de logon único do Zscaler](zscaler-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Zscaler no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Zscaler**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler**.

    ![O link do Zscaler na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** da sua conta do Zscaler, conforme descrito na etapa 6.

6. Para obter a **URL do locatário** e o **token secreto**, navegue até **Administração > configurações de autenticação** na interface do usuário do portal do Zscaler e clique em **SAML** em **tipo de autenticação**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar SAML** para abrir as opções de **configuração de SAML** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecione **habilitar provisionamento baseado em scim** para recuperar a **URL base** e o **token**de portador e, em seguida, salve as configurações. Copie a **URL base** para a **URL do locatário**e o token de **portador** para o **token secreto** no portal do Azure.

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Zscaler. Se a conexão falhar, verifique se sua conta do Zscaler tem permissões de administrador e tente novamente.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/test-connection.png)

8. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Zscaler**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Zscaler na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Zscaler para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para Zscaler**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para o Zscaler na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Zscaler para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Zscaler, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou grupos que você deseja provisionar para o Zscaler escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Provisionamento do Zscaler](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
