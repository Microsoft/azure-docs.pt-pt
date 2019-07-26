---
title: 'Tutorial: Configurar o Zscaler dois para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Zscaler dois.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e254cae22e3991aebc23188b8947f0e7efd9c5f5
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515425"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler dois para o provisionamento automático de usuário

Neste tutorial, você aprenderá a configurar o Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente usuários e/ou grupos para Zscaler dois.

> [!NOTE]
> Este tutorial descreve um conector que é criado no serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, e as respostas para perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas descritas neste tutorial, você precisará do seguinte:

* Um inquilino do Azure AD.
* Um Zscaler dois locatários.
* Uma conta de usuário no Zscaler dois com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API Zscaler dois SCIM, que está disponível para contas corporativas.

## <a name="add-zscaler-two-from-the-gallery"></a>Adicionar Zscaler dois da Galeria

Antes de configurar o Zscaler dois para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Zscaler dois da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

![Selecione Azure Active Directory](common/select-azuread.png)

Vá para **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**:

![Aplicações empresariais](common/enterprise-applications.png)

Para adicionar um aplicativo, selecione **novo aplicativo** na parte superior da janela:

![Selecionar novo aplicativo](common/add-new-app.png)

Na caixa de pesquisa, digite **Zscaler dois**. Selecione **Zscaler dois** nos resultados e, em seguida, selecione **Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Atribuir usuários ao Zscaler dois

Os usuários do Azure AD precisam receber acesso aos aplicativos selecionados antes de poderem usá-los. No contexto do provisionamento automático de usuário, somente os usuários ou grupos atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso a Zscaler dois. Depois de decidir isso, você pode atribuir esses usuários e grupos a Zscaler dois seguindo as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Dicas importantes para atribuir usuários ao Zscaler dois

* Recomendamos que você atribua primeiro um único usuário do Azure AD ao Zscaler dois para testar a configuração automática de provisionamento de usuário. Você pode atribuir mais usuários e grupos mais tarde.

* Ao atribuir um usuário ao Zscaler dois, você precisa selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o provisionamento automático de usuário

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos no Zscaler dois com base em atribuições de usuário e de grupo no Azure AD.

> [!TIP]
> Talvez você também queira habilitar o logon único baseado em SAML para o Zscaler dois. Se você fizer isso, siga as instruções no [tutorial de logon único do Zscaler dois](zscaler-two-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, mas os dois recursos se complementam.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos** > empresariais**todos os aplicativos** > **Zscaler dois**:

    ![Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler dois**:

    ![Lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** :

    ![Zscaler dois provisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**:

    ![Definir o modo de provisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** de sua conta do Zscaler Two, conforme descrito na próxima etapa.

6. Para obter a **URL do locatário** e o **token secreto**, acesse **Administração** > **configurações de autenticação** no portal do Zscaler dois e selecione **SAML** em **tipo de autenticação**:

    ![Zscaler duas configurações de autenticação](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Selecione **Configurar SAML** para abrir a janela **Configurar SAML** :

    ![Configurar janela SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Selecione **habilitar provisionamento baseado em scim** e copie a **URL base** e o **token**de portador e salve as configurações. No portal do Azure, Cole a **URL base** na caixa **URL do locatário** e o token de **portador** na caixa **token secreto** .

7. Depois de inserir os valores nas caixas **URL do locatário** e **token secreto** , selecione **testar conexão** para verificar se o Azure AD pode se conectar ao Zscaler dois. Se a conexão falhar, verifique se sua conta do Zscaler duas tem permissões de administrador e tente novamente.

    ![Testar a ligação](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Na caixa **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione **Enviar uma notificação por email quando ocorrer uma falha**:

    ![Configurar email de notificação](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para ZscalerTwo**:

    ![Sincronizar usuários do Azure AD](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o Zscaler dois na seção mapeamentos de **atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Zscaler dois para operações de atualização. Selecione **salvar** para confirmar as alterações.

    ![Mapeamentos de Atributos](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para ZscalerTwo**:

    ![Sincronizar grupos do Azure AD](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD para Zscaler dois na seção mapeamentos de **atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Zscaler dois para operações de atualização. Selecione **salvar** para confirmar as alterações.

    ![Mapeamentos de Atributos](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de escopo, consulte as instruções no [tutorial filtro de escopo](./../active-directory-saas-scoping-filters.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o Zscaler dois, altere o **status de provisionamento** para **ativado** na seção **configurações** :

    ![Estado do Aprovisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Defina os usuários e/ou grupos que você deseja provisionar para Zscaler dois escolhendo os valores desejados em **escopo** na seção **configurações** :

    ![Valores de escopo](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisionar, selecione **salvar**:

    ![Selecione salvar](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e grupos definidos em **escopo** na seção **configurações** . A sincronização inicial demora mais do que as sincronizações subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode monitorar o progresso na seção **detalhes de sincronização** . Você também pode seguir links para um relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Zscaler dois.

Para obter informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
