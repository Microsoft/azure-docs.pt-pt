---
title: 'Tutorial: Configurar BlueJeans para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à BlueJeans.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77059042"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configurar BlueJeans para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados em BlueJeans e Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desa provisionamento automaticamente de utilizadores e/ou grupos a BlueJeans.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um inquilino da AD AZure
* Um inquilino BlueJeans com o plano [My Company](https://www.BlueJeans.com/pricing) ou melhor habilitado
* Uma conta de utilizador em BlueJeans com permissões de Administração

> [!NOTE]
> A integração de provisionamento Azure AD depende da [API BlueJeans,](https://BlueJeans.github.io/developer)que está disponível para as equipas BlueJeans no plano Standard ou melhor.

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando BlueJeans da galeria

Antes de configurar blueJeans para fornecimento automático de utilizadores com Azure AD, você precisa adicionar BlueJeans da galeria de aplicações AD AD Azure à sua lista de aplicações geridas saaS.

**Para adicionar BlueJeans da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira BlueJeans,** selecione **BlueJeans** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

    ![BlueJeans na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Atribuir utilizadores a BlueJeans

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram "atribuídos" a uma aplicação em AD Azure são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso aos BlueJeans. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à BlueJeans seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Dicas importantes para a atribuição de utilizadores a BlueJeans

* Recomenda-se que um único utilizador Azure AD seja atribuído à BlueJeans para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a BlueJeans, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configurar o fornecimento automático de utilizadores a BlueJeans

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em BlueJeans com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para BlueJeans, seguindo as instruções fornecidas no [tutorial de inscrição única blueJeans](bluejeans-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para blueJeans em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **BlueJeans**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **BlueJeans**.

    ![O link BlueJeans na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Na secção **Credenciais de Administração,** insira o nome de **utilizador Admin**e **a Palavra-passe de Administrador** da sua conta BlueJeans. Exemplos destes valores são:

   * No campo nome de **utilizador Admin,** povoe o nome de utilizador da conta de administração no seu inquilino BlueJeans. Exemplo: admin@contoso.com.

   * No campo **Palavra-Passe Admin,** povoe a palavra-passe correspondente ao nome de utilizador de administração.

6. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se aos BlueJeans. Se a ligação falhar, certifique-se de que a sua conta BlueJeans tem permissões de Administração e tente novamente.

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Clique em **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to BlueJeans**.

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD a BlueJeans na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em BlueJeans para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de prestação de Ad Azure para BlueJeans, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Defina os utilizadores e/ou grupos que deseja prestar aos BlueJeans, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Provisão BlueJeans](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad Azure em BlueJeans.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Os bluejeans não permitem nomes de utilizadores que excedam 30 caracteres.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
